# 🐳 Project 3: Dockerized Flask App

**Difficulty:** Intermediate | **Time:** 4-5 hours | **Module:** 08

---

## 🎯 Project Goal

Project 2 ke Flask Todo API ko Docker + Docker Compose mein migrate karo with PostgreSQL.

---

## 📁 Structure

```
docker_todo/
    ├── app.py
    ├── requirements.txt
    ├── Dockerfile
    ├── docker-compose.yml
    ├── nginx.conf
    ├── .env
    └── .dockerignore
```

---

## 💻 Code

```bash
mkdir ~/docker_todo && cd ~/docker_todo

# app.py (PostgreSQL version):
cat > app.py << 'EOF'
from flask import Flask, jsonify, request
import psycopg2
import os
from dotenv import load_dotenv

load_dotenv()

app = Flask(__name__)

def get_db():
    return psycopg2.connect(os.environ['DATABASE_URL'])

def init_db():
    conn = get_db()
    cur = conn.cursor()
    cur.execute("""
        CREATE TABLE IF NOT EXISTS todos (
            id SERIAL PRIMARY KEY,
            title VARCHAR(200) NOT NULL,
            description TEXT,
            done BOOLEAN DEFAULT FALSE,
            created_at TIMESTAMP DEFAULT NOW()
        )
    """)
    conn.commit()
    cur.close()
    conn.close()

@app.route('/health')
def health():
    try:
        conn = get_db()
        conn.close()
        return jsonify({'status': 'healthy', 'database': 'connected'})
    except Exception as e:
        return jsonify({'status': 'unhealthy', 'error': str(e)}), 500

@app.route('/api/todos', methods=['GET'])
def get_todos():
    conn = get_db()
    cur = conn.cursor()
    cur.execute("SELECT * FROM todos ORDER BY id")
    rows = cur.fetchall()
    todos = [{'id': r[0], 'title': r[1], 'description': r[2], 'done': r[3]} for r in rows]
    cur.close()
    conn.close()
    return jsonify(todos)

@app.route('/api/todos', methods=['POST'])
def create_todo():
    data = request.json
    conn = get_db()
    cur = conn.cursor()
    cur.execute(
        "INSERT INTO todos (title, description) VALUES (%s, %s) RETURNING id",
        (data['title'], data.get('description', ''))
    )
    todo_id = cur.fetchone()[0]
    conn.commit()
    cur.close()
    conn.close()
    return jsonify({'id': todo_id, 'title': data['title']}), 201

if __name__ == '__main__':
    with app.app_context():
        init_db()
    app.run(host='0.0.0.0', port=5000)
EOF

# requirements.txt:
cat > requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
psycopg2-binary==2.9.6
python-dotenv==0.21.0
EOF

# Dockerfile:
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1
CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
EOF

# .dockerignore:
cat > .dockerignore << 'EOF'
.git/
__pycache__/
*.pyc
venv/
.env
*.log
EOF

# .env:
cat > .env << 'EOF'
POSTGRES_USER=todouser
POSTGRES_PASSWORD=todopass123
POSTGRES_DB=tododb
DATABASE_URL=postgresql://todouser:todopass123@db:5432/tododb
SECRET_KEY=docker-secret-key
EOF

# nginx.conf:
cat > nginx.conf << 'EOF'
server {
    listen 80;
    server_name localhost;
    client_max_body_size 10M;

    location / {
        proxy_pass http://web:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_connect_timeout 60;
        proxy_read_timeout 60;
    }
}
EOF

# docker-compose.yml:
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  db:
    image: postgres:15-alpine
    container_name: todo_postgres
    env_file: .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks: [todo_net]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  web:
    build: .
    container_name: todo_flask
    env_file: .env
    depends_on:
      db:
        condition: service_healthy
    networks: [todo_net]
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    container_name: todo_nginx
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on: [web]
    networks: [todo_net]
    restart: unless-stopped

volumes:
  postgres_data:

networks:
  todo_net:
    driver: bridge
EOF

echo "All files created!"
ls -la
```

---

## 🚀 Run karo

```bash
cd ~/docker_todo

if command -v docker &>/dev/null; then
    # Build aur start:
    docker-compose up -d --build

    # Healthy hone ka wait karo:
    echo "Waiting for services..."
    sleep 15

    # DB init karo:
    docker-compose exec web python3 -c "
from app import app, init_db
with app.app_context():
    init_db()
    print('Database initialized!')
"

    # Test karo:
    curl http://localhost:8080/health
    curl http://localhost:8080/api/todos

    # Todo create karo:
    curl -X POST http://localhost:8080/api/todos \
      -H "Content-Type: application/json" \
      -d '{"title":"Dockerize Flask","description":"Done!"}'

    curl http://localhost:8080/api/todos | python3 -m json.tool

    # Logs:
    docker-compose logs web | tail -10

    # Status:
    docker-compose ps

    echo "Project 3 Complete!"
    docker-compose down
else
    echo "Docker required for this project"
fi
```

---

## ✅ Success Criteria

- [ ] docker-compose up se sab services start hote hain
- [ ] Flask + PostgreSQL connected hai
- [ ] Nginx reverse proxy kaam karta hai
- [ ] Data docker volume mein persist hota hai
- [ ] Health check endpoint kaam karta hai
- [ ] docker-compose down se sab gracefully stop hote hain

**Project 4: SSH + Server Setup! 🔐**
