# 🎼 Lesson 6: Docker Compose — Multi-Container Apps

---

## 📚 Learning Objectives

- Docker Compose kya hai
- docker-compose.yml likhna
- Multi-container apps manage karna
- Full-stack deployment (Flask + PostgreSQL + Redis + Nginx)

---

## 🎼 Docker Compose kya hai?

```
Problem: Multiple containers manage karna mushkil hai!
docker run -d --name postgres ...     (5 flags)
docker run -d --name redis ...        (3 flags)
docker run -d --name flask --link ... (10 flags!)

Solution: Docker Compose
Ek YAML file mein sab define karo
docker-compose up  ←  Sab kuch start!
docker-compose down ← Sab kuch stop!
```

---

## 📝 docker-compose.yml Structure

```yaml
version: '3.8'   # Compose file version

services:         # Containers define karo
  service_name:
    image: nginx                  # Ya build: ./
    ports:
      - "host:container"
    environment:
      - KEY=value
    volumes:
      - host_path:container_path
    networks:
      - my_network
    depends_on:
      - database
    restart: unless-stopped

volumes:          # Named volumes define
  db_data:

networks:         # Custom networks
  my_network:
    driver: bridge
```

---

## 🚀 Flask + PostgreSQL App

```bash
mkdir ~/compose_demo && cd ~/compose_demo

# 1. Flask App:
cat > app.py << 'EOF'
from flask import Flask, jsonify
import psycopg2
import os

app = Flask(__name__)

def get_db():
    return psycopg2.connect(os.environ.get('DATABASE_URL'))

@app.route('/')
def home():
    return jsonify({'message': 'Flask + PostgreSQL in Docker!'})

@app.route('/db-check')
def db_check():
    try:
        conn = get_db()
        conn.close()
        return jsonify({'database': 'connected!'})
    except Exception as e:
        return jsonify({'database': 'error', 'detail': str(e)}), 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

# 2. Requirements:
cat > requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
psycopg2-binary==2.9.6
EOF

# 3. Dockerfile:
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
EOF

# 4. docker-compose.yml (THE MAIN FILE!):
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:

  # PostgreSQL Database:
  db:
    image: postgres:15-alpine
    container_name: postgres_db
    environment:
      POSTGRES_USER: flaskuser
      POSTGRES_PASSWORD: flaskpass
      POSTGRES_DB: flaskdb
    volumes:
      - postgres_data:/var/lib/postgresql/data  # Persistent!
    networks:
      - app_network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U flaskuser"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache:
  redis:
    image: redis:7-alpine
    container_name: redis_cache
    networks:
      - app_network
    restart: unless-stopped

  # Flask Application:
  web:
    build: .
    container_name: flask_app
    environment:
      DATABASE_URL: postgresql://flaskuser:flaskpass@db:5432/flaskdb
      REDIS_URL: redis://redis:6379/0
      FLASK_ENV: production
      SECRET_KEY: your-secret-key-here
    ports:
      - "5000:5000"
    depends_on:
      db:
        condition: service_healthy  # DB healthy hone ke baad start!
      redis:
        condition: service_started
    networks:
      - app_network
    volumes:
      - ./logs:/app/logs
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Nginx (optional, frontend reverse proxy):
  nginx:
    image: nginx:alpine
    container_name: nginx_proxy
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web
    networks:
      - app_network
    restart: unless-stopped

volumes:
  postgres_data:   # Named volume (persistent!)

networks:
  app_network:
    driver: bridge
EOF

# 5. Nginx config:
cat > nginx.conf << 'EOF'
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://web:5000;  # Docker DNS - service name!
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
EOF

# 6. .env file (optional):
cat > .env << 'EOF'
POSTGRES_USER=flaskuser
POSTGRES_PASSWORD=flaskpass
POSTGRES_DB=flaskdb
SECRET_KEY=production-secret-key
EOF

echo "docker-compose.yml complete!"
```

---

## ⚡ Docker Compose Commands

```bash
# Start sab services (foreground):
docker-compose up

# Start background mein:
docker-compose up -d

# Build karo (agar Dockerfile change hua):
docker-compose up --build
docker-compose up -d --build

# Stop sab:
docker-compose down

# Stop + volumes delete:
docker-compose down -v   # Data bhi delete!

# Stop + images delete:
docker-compose down --rmi all

# Specific service start/stop:
docker-compose up -d db redis      # Sirf db aur redis
docker-compose stop web            # Sirf web stop

# Logs:
docker-compose logs                 # Sab services
docker-compose logs -f              # Live
docker-compose logs -f web          # Specific service
docker-compose logs --tail 50 web

# Services status:
docker-compose ps

# Scale (multiple instances!):
docker-compose up -d --scale web=3  # 3 Flask instances!

# Service mein execute:
docker-compose exec web bash
docker-compose exec db psql -U flaskuser -d flaskdb

# Rebuild single service:
docker-compose build web
docker-compose up -d --no-deps --build web

# Config validate:
docker-compose config
```

---

## 🗂️ Environment Variables in Compose

```yaml
# Option 1: .env file (auto-load!):
# .env:
# DB_PASS=secretpass

# docker-compose.yml:
services:
  db:
    environment:
      POSTGRES_PASSWORD: ${DB_PASS}  # .env se aayega!

# Option 2: env_file:
services:
  web:
    env_file:
      - .env
      - .env.production

# Option 3: Direct:
services:
  web:
    environment:
      - SECRET_KEY=hardcoded   # Bad practice!
```

---

## 🏋️ Practice

```bash
if command -v docker &>/dev/null; then
    cd ~/compose_demo 2>/dev/null || (mkdir ~/compose_demo && cd ~/compose_demo)

    # Simple compose test:
    cat > docker-compose.test.yml << 'COMPEOF'
version: '3.8'
services:
  web:
    image: nginx:alpine
    ports:
      - "8095:80"
  whoami:
    image: traefik/whoami
    ports:
      - "8096:80"
COMPEOF

    docker-compose -f docker-compose.test.yml up -d
    sleep 3
    curl http://localhost:8095
    curl http://localhost:8096
    docker-compose -f docker-compose.test.yml down
    echo "Compose practice complete!"
else
    echo "Docker not available"
fi
```

---

## 📖 Summary

```
docker-compose.yml     = Multi-service config
docker-compose up -d   = Sab services start (background)
docker-compose down    = Sab stop
docker-compose down -v = Stop + delete volumes
docker-compose logs -f = Live logs
docker-compose ps      = Services status
docker-compose exec    = Service mein command run
docker-compose build   = Images rebuild
depends_on             = Service ordering
volumes                = Persistent storage
networks               = Container communication
```

---

**Next:** `08-Docker/Exercises.md` → Docker practice! 🐳
