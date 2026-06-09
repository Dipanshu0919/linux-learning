# 🌐 Lesson 5: Docker Networks — Container Communication

---

## 📚 Learning Objectives

- Docker network types samjhna
- Containers ko communicate karwana
- Custom networks banana
- Port mapping

---

## 🌐 Docker Network Types

```
1. bridge  = Default, containers same host pe communicate
2. host    = Container host ka network use kare
3. none    = No networking
4. overlay = Multi-host (Swarm/Kubernetes)
5. custom  = User-defined bridge (BEST for apps!)
```

---

## 🔗 Default Bridge Network

```bash
# Default bridge se dono containers run karo:
docker run -d --name container1 nginx
docker run -d --name container2 nginx

# Default bridge mein containers IP se communicate karte hain:
docker exec container1 ping 172.17.0.3  # IP se (fragile!)
# Docker DNS kaam nahi karta default bridge mein!
```

---

## 🏗️ Custom Network — Recommended!

```bash
# Custom network banao:
docker network create myapp_net
docker network create --driver bridge myapp_net

# Containers custom network pe:
docker run -d --name database \
    --network myapp_net \
    -e POSTGRES_PASSWORD=secret \
    postgres:15-alpine

docker run -d --name webapp \
    --network myapp_net \
    -p 5000:5000 \
    myflaskapp

# Ab webapp, "database" naam se PostgreSQL access kar sakta hai!
# DATABASE_URL = postgresql://postgres:secret@database:5432/mydb
#                                               ^^^^^^^^
#                                           Container name as DNS!
```

---

## 🔧 Network Commands

```bash
# Networks list:
docker network ls

# Network details:
docker network inspect myapp_net

# Container ko network se connect/disconnect:
docker network connect myapp_net container1
docker network disconnect myapp_net container1

# Network delete:
docker network rm myapp_net
docker network prune   # Unused networks delete
```

---

## 🏋️ Practice

```bash
if command -v docker &>/dev/null; then
    # 1. Custom network:
    docker network create demo_net

    # 2. Containers on same network:
    docker run -d --name web_server \
        --network demo_net \
        nginx:alpine

    docker run -d --name test_client \
        --network demo_net \
        alpine tail -f /dev/null

    # 3. DNS resolution (naam se):
    docker exec test_client ping -c 2 web_server
    docker exec test_client wget -q -O- http://web_server | head -3

    # 4. Cleanup:
    docker stop web_server test_client
    docker rm web_server test_client
    docker network rm demo_net
    echo "Network practice complete!"
fi
```

---

## 📖 Summary

```
docker network create name    = Custom network
docker network ls             = Networks list
--network mynet               = Container ko network se
docker network inspect        = Details
Container DNS                 = Service name se communicate!
Custom network > Default      = Always use custom!
```

**Next:** `08-Docker/06-Docker-Compose.md` → Multi-container mastery! 🎼
