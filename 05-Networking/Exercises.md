# 🏋️ 05-Networking: Exercises

---

## Exercise 1: Network Investigation

```bash
# 1. IP addresses:
ip addr show
ip a | grep "inet " | grep -v "127.0.0.1"

# 2. Routing:
ip route show

# 3. DNS:
cat /etc/resolv.conf
cat /etc/hosts

# 4. Active connections:
ss -tulpn

# 5. Hostname:
hostname
hostname -I    # All IPs
```

---

## Exercise 2: API Testing with curl

```bash
# 1. JSONPlaceholder API test:
curl -s https://jsonplaceholder.typicode.com/todos/1 | python3 -m json.tool

# 2. Multiple requests:
for i in 1 2 3; do
    echo "=== Post $i ==="
    curl -s https://jsonplaceholder.typicode.com/posts/$i | python3 -c "
import json, sys
d = json.load(sys.stdin)
print('Title:', d['title'][:50])
"
done

# 3. POST request:
curl -s -X POST \
  https://jsonplaceholder.typicode.com/posts \
  -H "Content-Type: application/json" \
  -d '{"title":"My Linux Post","body":"Learning Linux!","userId":1}' \
  | python3 -m json.tool
```

---

## Exercise 3: SSH Key Setup

```bash
# Complete SSH key workflow:

# 1. Generate key:
ssh-keygen -t ed25519 -C "linux.learning@test.com" \
  -f ~/.ssh/linux_course_key -N ""

# 2. View keys:
ls -la ~/.ssh/linux_course_key*
cat ~/.ssh/linux_course_key.pub

# 3. Config entry:
cat >> ~/.ssh/config << 'EOF'

Host linux-practice
    HostName localhost
    User codespace
    IdentityFile ~/.ssh/linux_course_key
EOF

chmod 600 ~/.ssh/config
cat ~/.ssh/config

# 4. Key fingerprint:
ssh-keygen -lf ~/.ssh/linux_course_key.pub
```

---

## Exercise 4: rsync Practice

```bash
# Setup:
mkdir -p /tmp/rsync_test/{source,backup}
for i in 1 2 3 4 5; do
    echo "Content of file $i - $(date)" > /tmp/rsync_test/source/file$i.txt
done
mkdir /tmp/rsync_test/source/logs
echo "Log entry 1" > /tmp/rsync_test/source/logs/app.log

# 1. Initial sync:
rsync -avz /tmp/rsync_test/source/ /tmp/rsync_test/backup/
ls -la /tmp/rsync_test/backup/

# 2. Modify a file:
echo "MODIFIED CONTENT" >> /tmp/rsync_test/source/file1.txt

# 3. Sync again (only changed):
rsync -avz /tmp/rsync_test/source/ /tmp/rsync_test/backup/

# 4. With exclusions:
rsync -avz --exclude="logs/" /tmp/rsync_test/source/ /tmp/rsync_test/backup2/ 2>/dev/null || (mkdir -p /tmp/rsync_test/backup2 && rsync -avz --exclude="logs/" /tmp/rsync_test/source/ /tmp/rsync_test/backup2/)
ls /tmp/rsync_test/backup2/    # logs/ nahi hona chahiye
```

---

## 🔥 Mini Project: Network Health Check Script

```bash
cat > ~/network_check.sh << 'SCRIPTEOF'
#!/bin/bash
echo "=== NETWORK HEALTH CHECK ==="
echo "Time: $(date)"
echo ""

# IP Address
echo "--- Local IPs ---"
ip addr | grep "inet " | grep -v "127.0.0.1" | awk '{print $2}' | while read ip; do
    echo "  $ip"
done

# DNS Resolution Test
echo ""
echo "--- DNS Resolution ---"
for domain in google.com python.org github.com; do
    if host $domain > /dev/null 2>&1; then
        echo "  ✓ $domain - OK"
    else
        echo "  ✗ $domain - FAILED"
    fi
done

# Open Ports
echo ""
echo "--- Listening Ports ---"
ss -tulpn | grep LISTEN | awk '{print "  " $5}' | sort -u

echo ""
echo "=== END ==="
SCRIPTEOF

chmod +x ~/network_check.sh
~/network_check.sh
```

---

## ✅ Self-Check

- [ ] ip addr se network interfaces dekh sakta/sakti hoon
- [ ] ss -tulpn se open ports dekh sakta/sakti hoon
- [ ] curl se API requests kar sakta/sakti hoon
- [ ] SSH key pair generate kar sakta/sakti hoon
- [ ] ~/.ssh/config configure kar sakta/sakti hoon
- [ ] rsync se files sync kar sakta/sakti hoon

**Next Module:** `06-Python-On-Linux` → Python + Linux = Power! 🐍
