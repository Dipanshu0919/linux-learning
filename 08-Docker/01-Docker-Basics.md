# 🐳 Lesson 1: Docker Basics — Containers ki Duniya!

---

## 📚 Learning Objectives

- Docker kya hai aur kyun use karte hain
- Containers vs Virtual Machines
- Docker architecture samjhna
- Docker install aur setup
- Hello World Docker se

---

## 🐳 Docker kya hai?

**Docker = Application packaging + running tool (Containers mein)**

```
Problem without Docker:
  "My machine pe kaam karta tha!" 😤
  Dev: Python 3.11, Ubuntu 22, Flask 2.3
  Prod: Python 3.8, CentOS 7, Flask 2.0
  → Different environment = Different results!

Solution with Docker:
  App + Dependencies + OS libraries → ONE Container
  Run anywhere: Dev, Test, Production = Same result! 🎉
```

---

## 📦 Containers vs Virtual Machines

```
VIRTUAL MACHINES:
┌─────────────────────────────┐
│  App A  │  App B  │  App C  │
│─────────│─────────│─────────│
│ Guest   │ Guest   │ Guest   │
│ OS      │ OS      │ OS      │ ← Heavy! (GBs each!)
│─────────────────────────────│
│         Hypervisor          │
│─────────────────────────────│
│      Host Operating System  │
└─────────────────────────────┘

DOCKER CONTAINERS:
┌─────────────────────────────┐
│  App A  │  App B  │  App C  │
│─────────│─────────│─────────│
│  Libs   │  Libs   │  Libs   │ ← Light! (MBs only!)
│─────────────────────────────│
│      Docker Engine          │
│─────────────────────────────│
│      Host Operating System  │
└─────────────────────────────┘

Containers = Lightweight, Fast, Portable
VMs = Heavy, Slower, Full OS isolation
```

---

## 🏗️ Docker Architecture

```
Docker Client (CLI: docker command)
      ↕ REST API
Docker Daemon (dockerd) — Main engine
      ├── Images  (Templates)
      ├── Containers (Running instances)
      ├── Volumes (Persistent storage)
      └── Networks (Container networking)
      
Registry (Docker Hub)
└── Public images: nginx, python, ubuntu, postgres...
```

---

## 📦 Docker Install karna

```bash
# Ubuntu mein:
# Old version hata do:
sudo apt-get remove docker docker-engine docker.io containerd runc

# Dependencies:
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release

# Docker GPG key:
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Repository add:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install:
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# User ko docker group mein add karo (sudo se bachne ke liye):
sudo usermod -aG docker $USER
newgrp docker

# Test:
docker --version
docker run hello-world
```

---

## 🎉 Hello World — Pehla Container!

```bash
# Hello World container:
docker run hello-world
```

```
Output:
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### Kya hua step by step:
```
1. docker run hello-world
2. Local mein hello-world image nahi mili
3. Docker Hub se download ki
4. Container create kiya
5. Container chala (message print hua)
6. Container band ho gaya
```

---

## 🔑 Key Concepts

```
Image    = Template (blueprint) — immutable
Container = Running instance of an image — mutable
Registry  = Image store (Docker Hub)
Dockerfile = Recipe to build an image
Volume   = Persistent storage for containers
Network  = Container ka networking
```

---

## ⚡ Basic Docker Commands (Overview)

```bash
# Images:
docker images               # Local images list
docker pull nginx           # Image download
docker rmi nginx            # Image delete

# Containers:
docker ps                   # Running containers
docker ps -a                # All containers
docker run nginx            # Container run
docker stop container_id    # Stop
docker rm container_id      # Remove

# Inspect:
docker inspect container_id
docker logs container_id
docker exec -it container bash

# System:
docker system df            # Disk usage
docker system prune         # Cleanup
```

---

## 🏋️ Practice

```bash
# 1. Docker version check:
docker --version
docker info

# 2. Hello World:
docker run hello-world

# 3. Ubuntu container:
docker run -it ubuntu:22.04 bash
# Container ke andar:
whoami          # root
cat /etc/os-release
ls /
exit            # Bahar aao

# 4. Images dekho:
docker images

# 5. Cleanup:
docker container prune -f   # Stopped containers remove
```

---

## 📖 Summary

```
Docker    = App containerization platform
Container = Lightweight isolated running process
Image     = Container ka template/blueprint
Registry  = Image store (Docker Hub)
docker run image   = Container run karo
docker ps          = Running containers
docker images      = Local images
docker pull image  = Download image
docker stop cid    = Stop container
docker rm cid      = Remove container
```

---

**Next:** `08-Docker/02-Images.md` → Docker images master karo! 🖼️
