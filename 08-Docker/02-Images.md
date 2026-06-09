# 🖼️ Lesson 2: Docker Images — Templates Master Karo

---

## 📚 Learning Objectives

- Docker images kya hain
- Dockerfile likhna
- Image build karna
- Docker Hub use karna
- Best practices

---

## 🖼️ Docker Image kya hai?

```
Image = Read-only template
      = Layers ka stack
      = Container ka blueprint

FROM ubuntu:22.04     ← Base layer
RUN apt-get install   ← New layer
COPY app.py /app/     ← New layer
CMD ["python3","app.py"] ← Config layer

Har instruction = New layer!
Layers shared hote hain = Space efficient
```

---

## 📝 Dockerfile — Image Recipe

```dockerfile
# Base image (FROM is always first):
FROM python:3.11-slim

# Metadata:
LABEL maintainer="you@example.com"
LABEL version="1.0"

# Environment variables:
ENV PYTHONUNBUFFERED=1
ENV FLASK_ENV=production

# Working directory:
WORKDIR /app

# Dependencies first (for caching!):
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# App code copy:
COPY . .

# Port expose karo (documentation):
EXPOSE 5000

# Healthcheck:
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1

# Run command:
CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
```

---

## 🔑 Dockerfile Instructions

```dockerfile
FROM image:tag          # Base image choose karo
RUN command             # Container mein command run (build time)
CMD ["cmd", "arg"]      # Default command (run time)
ENTRYPOINT ["cmd"]      # Main command (override hona mushkil)
COPY src dest           # Host se container mein copy
ADD src dest            # COPY + URL/tar support
ENV KEY=value           # Environment variable
ARG name=default        # Build-time variable
WORKDIR /path           # Working directory set
EXPOSE port             # Port document karo
VOLUME /path            # Volume mount point
USER username           # User switch karo
HEALTHCHECK             # Health check command
LABEL key=value         # Metadata
```

---

## 🏗️ Flask App ka Dockerfile

```bash
# Project setup:
mkdir ~/docker_flask && cd ~/docker_flask

# App files:
cat > app.py << 'EOF'
from flask import Flask, jsonify
app = Flask(__name__)

@app.route('/')
def home():
    return jsonify({'message': 'Flask in Docker!', 'status': 'running'})

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

cat > requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
EOF

# Dockerfile:
cat > Dockerfile << 'EOF'
FROM python:3.11-slim

# System packages (agar chahiye):
RUN apt-get update && apt-get install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Non-root user banao (security!):
RUN useradd -m -u 1001 appuser

WORKDIR /app

# Dependencies first (Docker layer caching):
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# App code:
COPY app.py .

# User switch:
USER appuser

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=5s \
    CMD curl -f http://localhost:5000/health || exit 1

CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
EOF

# .dockerignore banao:
cat > .dockerignore << 'EOF'
.git/
.gitignore
__pycache__/
*.pyc
venv/
.env
*.log
README.md
EOF
```

---

## 🔨 Image Build karna

```bash
# Build karo:
docker build -t myflaskapp:1.0 .

# Tags ke saath:
docker build -t myflaskapp:latest -t myflaskapp:1.0 .

# Build args ke saath:
docker build --build-arg ENV=production -t myflaskapp .

# No cache (fresh build):
docker build --no-cache -t myflaskapp .

# Verbose:
docker build --progress=plain -t myflaskapp .
```

### Build output:
```
[+] Building 15.2s
 => [internal] load build definition from Dockerfile
 => [1/5] FROM python:3.11-slim
 => [2/5] RUN apt-get update...
 => [3/5] COPY requirements.txt .
 => [4/5] RUN pip install -r requirements.txt
 => [5/5] COPY app.py .
 => exporting to image
 => naming to myflaskapp:1.0
```

---

## 🚀 Image Run karna

```bash
# Basic run:
docker run myflaskapp:1.0

# Port expose karo:
docker run -p 5000:5000 myflaskapp:1.0

# Background (detached):
docker run -d -p 5000:5000 --name flask_container myflaskapp:1.0

# Test:
curl http://localhost:5000/
curl http://localhost:5000/health

# Container logs:
docker logs flask_container

# Stop aur remove:
docker stop flask_container
docker rm flask_container
```

---

## 🌐 Docker Hub — Images Share Karo

```bash
# Docker Hub account chahiye: hub.docker.com

# Login:
docker login

# Image tag karo (username/repo:tag format):
docker tag myflaskapp:1.0 yourusername/myflaskapp:1.0
docker tag myflaskapp:1.0 yourusername/myflaskapp:latest

# Push karo:
docker push yourusername/myflaskapp:1.0
docker push yourusername/myflaskapp:latest

# Kisi bhi machine se pull:
docker pull yourusername/myflaskapp:latest
docker run -p 5000:5000 yourusername/myflaskapp:latest
```

---

## 📊 Image Management

```bash
# Images list:
docker images
docker image ls

# Image details:
docker inspect myflaskapp:1.0
docker history myflaskapp:1.0  # Layers dekho

# Image size:
docker images | grep myflaskapp

# Tag add karo:
docker tag myflaskapp:1.0 myflaskapp:stable

# Image delete:
docker rmi myflaskapp:1.0
docker image rm myflaskapp:1.0

# Unused images cleanup:
docker image prune
docker image prune -a  # All unused
```

---

## 🏋️ Practice

```bash
cd ~/docker_flask 2>/dev/null || (mkdir ~/docker_flask && cd ~/docker_flask)

# Already setup karo tha toh:
docker build -t mypractice:1.0 . 2>/dev/null || echo "Need to setup files first"

# Agar docker available hai:
if command -v docker &>/dev/null; then
    # Ubuntu:22.04 pull:
    docker pull ubuntu:22.04

    # Images dekho:
    docker images

    # Simple nginx run karo:
    docker run -d -p 8090:80 --name my_nginx nginx
    sleep 2
    curl http://localhost:8090
    docker stop my_nginx
    docker rm my_nginx

    # Cleanup:
    docker image prune -f
    echo "Docker practice complete!"
else
    echo "Docker not available, check installation"
fi
```

---

## 📖 Summary

```
Dockerfile         = Image banane ki recipe
FROM               = Base image
RUN                = Build-time command
COPY               = Files copy
CMD / ENTRYPOINT   = Run command
EXPOSE             = Port document
docker build -t    = Image build karo
docker run -p      = Container run with port
docker images      = Local images list
docker push        = Docker Hub pe upload
docker pull        = Docker Hub se download
.dockerignore      = Files exclude karo (gitignore jaisa)
```

---

**Next:** `08-Docker/03-Containers.md` → Containers master karo! 📦
