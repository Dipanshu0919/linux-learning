# 📦 Lesson 3: Docker Containers — Running Instances

---

## 📚 Learning Objectives

- Container lifecycle samjhna
- Container management commands
- Container mein interact karna
- Logs aur debugging
- Container networking basics

---

## 🔄 Container Lifecycle

```
docker create  →  Created
docker start   →  Running
docker pause   →  Paused
docker stop    →  Stopped
docker rm      →  Removed

docker run = create + start (ek saath!)
```

---

## ⚡ Container Commands

### Create aur Run:
```bash
# Basic run:
docker run nginx

# Background (Detached mode):
docker run -d nginx

# Name do:
docker run -d --name my_nginx nginx

# Port mapping:
docker run -d -p 8080:80 nginx
# HOST_PORT:CONTAINER_PORT

# Multiple ports:
docker run -d -p 8080:80 -p 4430:443 nginx

# Environment variables:
docker run -d \
  -e DATABASE_URL=postgresql://localhost/db \
  -e SECRET_KEY=mysecret \
  --name myapp myflaskapp

# Auto remove after stop:
docker run --rm nginx

# Interactive terminal:
docker run -it ubuntu:22.04 bash

# Resource limits:
docker run -d \
  --memory="512m" \
  --cpus="1.5" \
  nginx

# Restart policy:
docker run -d --restart always nginx          # Hamesha restart
docker run -d --restart unless-stopped nginx  # Manually stop se nahi
docker run -d --restart on-failure:5 nginx    # Failure pe, max 5 times
```

---

### Container Dekho:
```bash
# Running containers:
docker ps

# All containers (stopped bhi):
docker ps -a

# Specific info:
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Container details:
docker inspect container_name
docker inspect container_name | grep IPAddress
```

---

### Container Control:
```bash
# Stop (graceful - SIGTERM):
docker stop my_nginx
docker stop -t 30 my_nginx  # 30 sec timeout

# Kill (force - SIGKILL):
docker kill my_nginx

# Start stopped container:
docker start my_nginx

# Restart:
docker restart my_nginx

# Pause (freeze):
docker pause my_nginx

# Resume:
docker unpause my_nginx

# Remove:
docker rm my_nginx                # Stopped hona chahiye
docker rm -f my_nginx             # Force (running bhi hata sakta hai)

# Sab stopped containers remove:
docker container prune
docker rm $(docker ps -aq -f status=exited)  # Same
```

---

### Container mein Interact karna:
```bash
# Running container mein bash:
docker exec -it container_name bash

# Single command run:
docker exec container_name ls /app
docker exec container_name cat /etc/nginx/nginx.conf

# Container mein file copy:
docker cp local_file.txt container_name:/app/
docker cp container_name:/app/logs/app.log ./local_logs/

# Container ke environment variables:
docker exec container_name env
```

---

## 📊 Container Logs

```bash
# Logs dekho:
docker logs container_name

# Follow (live):
docker logs -f container_name

# Last N lines:
docker logs --tail 50 container_name

# Timestamps ke saath:
docker logs -t container_name

# Time range:
docker logs --since "2h" container_name   # Last 2 hours
docker logs --until "1h" container_name

# Combine:
docker logs -f --tail 100 container_name
```

---

## 📈 Container Stats

```bash
# Live stats:
docker stats
docker stats container_name

# One-time snapshot:
docker stats --no-stream

# Top processes:
docker top container_name
```

Output:
```
CONTAINER ID   NAME       CPU %   MEM USAGE    NET I/O    BLOCK I/O
abc123         my_nginx   0.1%    5.2MB/512MB  1kB/500B   0B/0B
def456         flask_app  2.3%    45MB/512MB   15kB/8kB   12kB/0B
```

---

## 🌐 Container Networking Basics

```bash
# Default network (bridge):
docker run -d nginx
docker inspect nginx | grep IPAddress
# Container ka IP: 172.17.0.2 (bridge network)

# Host network (container = host network):
docker run -d --network host nginx
# Ab localhost:80 seedha nginx se connect!

# Container-to-container (same network):
docker network create mynet
docker run -d --name db --network mynet postgres
docker run -d --name app --network mynet myflaskapp
# App "db" naam se database access kar sakta hai!
```

---

## 🏋️ Practice

```bash
if command -v docker &>/dev/null; then
    # 1. Named container run karo:
    docker run -d --name practice_nginx -p 8091:80 nginx
    docker ps | grep practice_nginx
    curl http://localhost:8091

    # 2. Container mein explore karo:
    docker exec practice_nginx nginx -v
    docker exec practice_nginx ls /etc/nginx/

    # 3. Logs:
    docker logs practice_nginx
    curl http://localhost:8091  # Access karo
    docker logs --tail 5 practice_nginx

    # 4. Stats:
    docker stats --no-stream practice_nginx

    # 5. Cleanup:
    docker stop practice_nginx
    docker rm practice_nginx
    echo "Container practice complete!"
else
    echo "Docker not available"
fi
```

---

## 📖 Summary

```
docker run -d           = Background container run
docker run -it          = Interactive (bash access)
docker run -p H:C       = Port mapping
docker run -e KEY=val   = Environment variable
docker run --name       = Container naam do
docker ps               = Running containers
docker ps -a            = All containers
docker stop/start/rm    = Lifecycle management
docker exec -it bash    = Container mein bash
docker logs -f          = Live logs
docker stats            = Resource usage
docker cp               = Files copy in/out
```

---

**Next:** `08-Docker/04-Volumes.md` → Data persistence! 💾
