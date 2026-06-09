# ⚙️ Project 5: Mini DevOps Pipeline

**Difficulty:** Advanced | **Time:** 6-8 hours | **Module:** 06-08

---

## 🎯 Project Goal

Ek mini CI/CD pipeline banao: code push karo → automatic test → build Docker image → deploy karo.

---

## 🏗️ Pipeline Architecture

```
Developer pushes code
        ↓
GitHub Actions / Shell Script triggers
        ↓
Tests run (pytest)
        ↓
Docker image build
        ↓
Image push to registry
        ↓
Server pe deploy (docker-compose pull + restart)
        ↓
Health check
        ↓
Success / Rollback
```

---

## 📦 Part 1: Flask App with Tests

```bash
mkdir ~/devops_pipeline && cd ~/devops_pipeline

# App:
cat > app.py << 'EOF'
from flask import Flask, jsonify, request

app = Flask(__name__)

items = {}
next_id = 1

@app.route('/health')
def health():
    return jsonify({'status': 'ok', 'version': '1.0'})

@app.route('/items', methods=['GET'])
def list_items():
    return jsonify(list(items.values()))

@app.route('/items', methods=['POST'])
def add_item():
    global next_id
    data = request.json
    if not data or 'name' not in data:
        return jsonify({'error': 'name required'}), 400
    item = {'id': next_id, 'name': data['name'], 'qty': data.get('qty', 0)}
    items[next_id] = item
    next_id += 1
    return jsonify(item), 201

@app.route('/items/<int:item_id>', methods=['DELETE'])
def remove_item(item_id):
    if item_id not in items:
        return jsonify({'error': 'not found'}), 404
    del items[item_id]
    return jsonify({'deleted': item_id})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

# Tests:
cat > test_app.py << 'EOF'
import pytest
from app import app

@pytest.fixture
def client():
    app.testing = True
    with app.test_client() as c:
        yield c

def test_health(client):
    r = client.get('/health')
    assert r.status_code == 200
    assert r.json['status'] == 'ok'

def test_empty_items(client):
    r = client.get('/items')
    assert r.status_code == 200
    assert r.json == []

def test_add_item(client):
    r = client.post('/items',
        json={'name': 'Apple', 'qty': 5},
        content_type='application/json')
    assert r.status_code == 201
    assert r.json['name'] == 'Apple'
    assert r.json['qty'] == 5

def test_add_item_no_name(client):
    r = client.post('/items',
        json={'qty': 5},
        content_type='application/json')
    assert r.status_code == 400

def test_delete_item(client):
    r = client.post('/items',
        json={'name': 'Banana'},
        content_type='application/json')
    item_id = r.json['id']
    r2 = client.delete(f'/items/{item_id}')
    assert r2.status_code == 200
    assert r2.json['deleted'] == item_id

def test_delete_nonexistent(client):
    r = client.delete('/items/9999')
    assert r.status_code == 404
EOF

cat > requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
pytest==7.3.0
pytest-cov==4.0.0
EOF

cat > requirements-dev.txt << 'EOF'
-r requirements.txt
pytest==7.3.0
pytest-cov==4.0.0
EOF
```

---

## 🐳 Part 2: Dockerfile + Compose

```bash
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
LABEL maintainer="you@example.com"

RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd -m appuser && chown -R appuser /app
USER appuser

EXPOSE 5000
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1

CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
EOF

cat > docker-compose.yml << 'EOF'
version: '3.8'
services:
  app:
    build: .
    image: devops-app:latest
    container_name: devops_app
    ports: ["5000:5000"]
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
EOF
```

---

## 🤖 Part 3: Automated Pipeline Script

```bash
cat > pipeline.sh << 'SCRIPTEOF'
#!/bin/bash
set -e
VERSION=${1:-"latest"}
APP_NAME="devops-app"
COMPOSE_FILE="docker-compose.yml"
LOGFILE="pipeline_${VERSION}.log"

log() { echo "[$(date '+%H:%M:%S')] $1" | tee -a $LOGFILE; }
fail() { log "FAILED: $1"; exit 1; }

log "==================================================="
log "  DEPLOYMENT PIPELINE — Version: $VERSION"
log "==================================================="

# ─── Stage 1: Tests ─────────────────────────────────
log ""
log "STAGE 1: Running Tests"
python3 -m venv /tmp/test_venv > /dev/null 2>&1
source /tmp/test_venv/bin/activate
pip install -q -r requirements.txt pytest pytest-cov

pytest test_app.py -v --tb=short 2>&1 | tee -a $LOGFILE
TEST_EXIT=${PIPESTATUS[0]}
deactivate

if [ $TEST_EXIT -ne 0 ]; then
    fail "Tests failed! Aborting deployment."
fi
log "✓ All tests passed!"

# ─── Stage 2: Code Quality ──────────────────────────
log ""
log "STAGE 2: Code Quality Checks"
source /tmp/test_venv/bin/activate
if pip install -q flake8; then
    flake8 app.py --max-line-length=100 && log "✓ flake8 passed!" || log "⚠ flake8 warnings (non-blocking)"
fi
deactivate

# ─── Stage 3: Docker Build ──────────────────────────
log ""
log "STAGE 3: Building Docker Image"
if command -v docker &>/dev/null; then
    docker build -t ${APP_NAME}:${VERSION} -t ${APP_NAME}:latest . 2>&1 | tail -5 | tee -a $LOGFILE
    log "✓ Image built: ${APP_NAME}:${VERSION}"
else
    log "⚠ Docker not available — skipping build stage"
fi

# ─── Stage 4: Deploy ────────────────────────────────
log ""
log "STAGE 4: Deploying"
if command -v docker &>/dev/null; then
    docker-compose up -d --build 2>&1 | tail -5 | tee -a $LOGFILE || log "⚠ Compose deploy skipped"
    sleep 5

    # ─── Stage 5: Health Check ──────────────────────────
    log ""
    log "STAGE 5: Health Check"
    if curl -sf http://localhost:5000/health > /dev/null 2>&1; then
        log "✓ Application is healthy!"
        HEALTH=$(curl -s http://localhost:5000/health)
        log "   Response: $HEALTH"
    else
        log "✗ Health check failed — rolling back!"
        docker-compose down
        fail "Deployment failed health check"
    fi
fi

log ""
log "==================================================="
log "  PIPELINE COMPLETE — SUCCESS! ✓"
log "==================================================="
log "Log file: $LOGFILE"
SCRIPTEOF

chmod +x pipeline.sh
```

---

## 🔁 Part 4: GitHub Actions Workflow

```bash
mkdir -p .github/workflows

cat > .github/workflows/deploy.yml << 'EOF'
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt pytest pytest-cov

      - name: Run tests
        run: pytest test_app.py -v --cov=app --cov-report=term

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3

      - name: Build Docker image
        run: docker build -t devops-app:${{ github.sha }} .

      - name: Test Docker image
        run: |
          docker run -d -p 5000:5000 --name test_container devops-app:${{ github.sha }}
          sleep 5
          curl -f http://localhost:5000/health
          docker stop test_container
          docker rm test_container

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: deploy
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /var/www/myapp
            git pull origin main
            docker-compose up -d --build
            sleep 5
            curl -f http://localhost:5000/health || exit 1
            echo "Deployment successful!"
EOF
```

---

## 🚀 Run the Pipeline

```bash
cd ~/devops_pipeline

# Install test dependencies:
pip3 install flask pytest pytest-cov

# Run pipeline (it runs tests automatically):
bash pipeline.sh v1.0.0

# Check log:
cat pipeline_v1.0.0.log
```

---

## ✅ Success Criteria

- [ ] Sab pytest tests pass hote hain
- [ ] pipeline.sh successfully complete hota hai
- [ ] GitHub Actions workflow file ready hai
- [ ] Docker image build hoti hai (agar Docker available)
- [ ] Health check pass hota hai
- [ ] Log file generate hota hai

**Project 6: Linux Admin Challenge! 🏆**
