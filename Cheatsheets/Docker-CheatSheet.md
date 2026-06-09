# 🐳 Docker Cheatsheet — Quick Reference

---

## 🖼️ Images

```bash
docker images                           # Local images list
docker pull nginx:latest                # Download image
docker pull python:3.11-slim
docker rmi image_name                   # Delete image
docker image prune                      # Unused images delete
docker history image_name               # Layer history
docker inspect image_name               # Full details

# Build:
docker build -t myapp:1.0 .            # Build from Dockerfile
docker build -t myapp:1.0 -f Dockerfile.prod .  # Custom Dockerfile
docker build --no-cache -t myapp .     # Fresh build

# Tag & Push:
docker tag myapp:1.0 user/myapp:1.0
docker push user/myapp:1.0
docker login
```

---

## 📦 Containers

```bash
# Run:
docker run nginx                        # Foreground
docker run -d nginx                     # Detached (background)
docker run -d -p 8080:80 nginx          # Port mapping
docker run -d --name my_nginx nginx     # Named container
docker run -d -e KEY=value nginx        # Env variable
docker run -it ubuntu bash              # Interactive terminal
docker run --rm nginx                   # Auto remove after stop
docker run -d --restart always nginx    # Always restart

# Manage:
docker ps                               # Running containers
docker ps -a                            # All containers
docker stop my_nginx                    # Graceful stop
docker kill my_nginx                    # Force stop
docker start my_nginx                   # Start stopped
docker restart my_nginx                 # Restart
docker rm my_nginx                      # Remove (stopped)
docker rm -f my_nginx                   # Force remove
docker container prune                  # Remove all stopped

# Interact:
docker exec -it my_nginx bash          # Shell access
docker exec my_nginx ls /etc/nginx     # Single command
docker logs my_nginx                   # View logs
docker logs -f my_nginx                # Follow logs
docker logs --tail 50 my_nginx         # Last 50 lines
docker cp local.txt my_nginx:/app/     # Copy file in
docker cp my_nginx:/app/log.txt ./     # Copy file out
docker stats my_nginx                  # Resource usage
docker top my_nginx                    # Processes
docker inspect my_nginx                # Full details
```

---

## 💾 Volumes

```bash
docker volume create mydata            # Create volume
docker volume ls                       # List volumes
docker volume inspect mydata           # Details
docker volume rm mydata                # Delete
docker volume prune                    # Remove unused

# Use volumes:
docker run -v mydata:/app/data nginx            # Named volume
docker run -v $(pwd):/app nginx                 # Bind mount
docker run -v /host/path:/container:ro nginx    # Read-only
```

---

## 🌐 Networks

```bash
docker network ls                      # List networks
docker network create mynet            # Custom network
docker network inspect mynet           # Details
docker network rm mynet                # Delete
docker network prune                   # Remove unused

# Connect container to network:
docker run --network mynet nginx
docker network connect mynet container1
docker network disconnect mynet container1
```

---

## 🎼 Docker Compose

```bash
docker-compose up                      # Start (foreground)
docker-compose up -d                   # Start (background)
docker-compose up -d --build           # Rebuild + start
docker-compose down                    # Stop + remove
docker-compose down -v                 # Stop + remove volumes
docker-compose down --rmi all          # Stop + remove images

docker-compose ps                      # Services status
docker-compose logs                    # All logs
docker-compose logs -f web             # Follow specific service
docker-compose logs --tail 50 web

docker-compose exec web bash           # Shell in service
docker-compose exec db psql -U user    # DB access

docker-compose restart web             # Restart service
docker-compose stop web                # Stop service
docker-compose start web               # Start service
docker-compose build web               # Rebuild service
docker-compose pull                    # Pull latest images

docker-compose config                  # Validate config
docker-compose scale web=3             # Scale service
```

---

## 🧹 Cleanup

```bash
docker system prune                    # Remove all unused
docker system prune -a                 # Remove all (including unused images)
docker system df                       # Disk usage

docker container prune                 # Stopped containers
docker image prune                     # Dangling images
docker image prune -a                  # All unused images
docker volume prune                    # Unused volumes
docker network prune                   # Unused networks
```

---

## 📝 Dockerfile Reference

```dockerfile
FROM python:3.11-slim           # Base image
LABEL key=value                 # Metadata
ENV KEY=value                   # Environment variable
ARG BUILD_VAR=default           # Build argument
WORKDIR /app                    # Working directory
COPY src dest                   # Copy files
ADD src dest                    # Copy + URL/tar support
RUN command                     # Build-time command
EXPOSE 5000                     # Document port
VOLUME /data                    # Volume mount point
USER username                   # Switch user
HEALTHCHECK --interval=30s CMD  # Health check
CMD ["cmd", "arg"]              # Default command
ENTRYPOINT ["cmd"]              # Main command
```

---

## 🐳 docker-compose.yml Template

```yaml
version: '3.8'

services:
  web:
    build: .
    image: myapp:latest
    container_name: myapp
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    env_file:
      - .env
    volumes:
      - ./logs:/app/logs
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app_network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myuser"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    networks:
      - app_network

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web
    networks:
      - app_network

volumes:
  postgres_data:

networks:
  app_network:
    driver: bridge
```
