# 💾 Lesson 4: Docker Volumes — Data Persistence

---

## 📚 Learning Objectives

- Volumes kya hain aur kyun chahiye
- Volume types samjhna
- Data persist karna
- Backup aur restore

---

## 💾 Volumes kyun chahiye?

```
PROBLEM:
Container delete karo → Sab data gone! 😱
Database run karo container mein → Container stop → Data lost!

SOLUTION: Volumes
Volume = Container ke bahar data store karo
Container delete ho → Volume data safe! ✅
```

---

## 📂 Volume Types

```
1. Named Volumes      = Docker managed, /var/lib/docker/volumes/
2. Bind Mounts        = Host ka specific folder map karo
3. tmpfs Mounts       = RAM mein (temporary, fast)
```

---

## 🗂️ Named Volumes

```bash
# Volume create:
docker volume create mydata

# Volume list:
docker volume ls

# Volume details:
docker volume inspect mydata

# Volume ke saath container run:
docker run -d \
  -v mydata:/var/lib/postgresql/data \
  --name mypostgres \
  postgres:15

# Data persist test:
docker exec mypostgres psql -U postgres -c "CREATE DATABASE testdb;"
docker stop mypostgres
docker rm mypostgres

# Naya container, same volume:
docker run -d \
  -v mydata:/var/lib/postgresql/data \
  --name mypostgres2 \
  postgres:15

# Data still there!
docker exec mypostgres2 psql -U postgres -c "\l"
# testdb dikhega!

# Volume delete:
docker volume rm mydata      # (containers use na kar rahe ho)
docker volume prune          # Unused volumes delete
```

---

## 📁 Bind Mounts — Development ke liye!

```bash
# Host folder container mein map karo:
docker run -d \
  -v $(pwd)/app:/app \       # Current dir → /app in container
  -v $(pwd)/logs:/app/logs \
  -p 5000:5000 \
  myflaskapp

# Read-only mount:
docker run -v $(pwd)/config:/app/config:ro myapp

# Development workflow (live reload!):
docker run -d \
  -v $(pwd):/app \
  -p 5000:5000 \
  myflaskapp

# Code change karo locally → Container mein reflect hoga!
```

---

## 🏋️ Practice

```bash
if command -v docker &>/dev/null; then
    # 1. Named volume create:
    docker volume create practice_vol
    docker volume ls

    # 2. Volume ke saath container:
    docker run -d --name vol_test \
        -v practice_vol:/data \
        ubuntu:22.04 \
        tail -f /dev/null

    # 3. Data write karo:
    docker exec vol_test bash -c "echo 'Hello from Docker Volume!' > /data/test.txt"
    docker exec vol_test cat /data/test.txt

    # 4. Container remove karo (data safe!):
    docker stop vol_test && docker rm vol_test

    # 5. Naya container, same volume:
    docker run --rm -v practice_vol:/data ubuntu:22.04 cat /data/test.txt
    # Data still there!

    # 6. Cleanup:
    docker volume rm practice_vol
    echo "Volume practice complete!"
fi

# Bind mount practice:
mkdir -p /tmp/bindtest
echo "Host file content" > /tmp/bindtest/hostfile.txt

if command -v docker &>/dev/null; then
    docker run --rm \
        -v /tmp/bindtest:/container_dir \
        ubuntu:22.04 \
        cat /container_dir/hostfile.txt
fi
```

---

## 📖 Summary

```
docker volume create name   = Named volume
docker volume ls            = Volumes list
-v name:/container/path     = Named volume use
-v /host:/container         = Bind mount
-v /host:/container:ro      = Read-only bind
docker volume prune         = Unused volumes delete
Named volume = Persistent data (DB, uploads)
Bind mount = Dev workflow (code sync)
```

**Next:** `08-Docker/05-Networks.md` → Container networking! 🌐
