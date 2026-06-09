# 🏆 Project 6: Linux Admin Challenge

**Difficulty:** Advanced | **Time:** Full day | **Module:** All

---

## 🎯 Project Goal

Ek complete multi-app production server setup karo — DNS se lekar monitoring tak — sab kuch ek server pe manage karo.

---

## 🏗️ Final Architecture

```
Internet
    ↓
Cloudflare (DNS + CDN + DDoS protection)
    ↓
Ubuntu 22.04 VPS
    ↓ Port 443
Nginx (SSL termination + Reverse Proxy)
    ├─→ blog.domain.com → Flask Blog (port 5001)
    ├─→ api.domain.com  → FastAPI (port 8001)
    └─→ domain.com      → Static Portfolio (files)

Databases (localhost only):
    ├── PostgreSQL 15 (port 5432)
    └── Redis 7 (port 6379)

Monitoring:
    └── Netdata (port 19999, localhost only)
```

---

## 📋 Challenge Tasks

### Task 1: System Foundation (30 min)

```bash
# Complete system setup — ek script mein:
cat > ~/challenge_setup.sh << 'SCRIPTEOF'
#!/bin/bash
set -e

echo "=== LINUX ADMIN CHALLENGE SETUP ==="

# Update:
apt-get update && apt-get upgrade -y

# Packages:
apt-get install -y python3 python3-pip python3-venv \
    nginx postgresql redis-server git curl ufw fail2ban htop

# UFW:
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh && ufw allow 80/tcp && ufw allow 443/tcp
ufw --force enable

# PostgreSQL — dono apps ke liye databases:
sudo -u postgres psql << 'SQLEOF'
CREATE USER bloguser WITH PASSWORD 'blogpass123';
CREATE DATABASE blogdb OWNER bloguser;
CREATE USER apiuser WITH PASSWORD 'apipass456';
CREATE DATABASE apidb OWNER apiuser;
GRANT ALL PRIVILEGES ON DATABASE blogdb TO bloguser;
GRANT ALL PRIVILEGES ON DATABASE apidb TO apiuser;
SQLEOF

echo "✓ Foundation complete!"
SCRIPTEOF
chmod +x ~/challenge_setup.sh
echo "Setup script ready!"
```

---

### Task 2: Flask Blog App (45 min)

```bash
# Minimal blog app:
mkdir -p /var/www/blog
cat > /var/www/blog/app.py << 'EOF'
from flask import Flask, jsonify, request
import os

app = Flask(__name__)

posts = [
    {'id': 1, 'title': 'Hello Linux!', 'content': 'First post on my Linux server!'},
    {'id': 2, 'title': 'Docker Rocks', 'content': 'Containers changed my life.'},
]

@app.route('/')
def home():
    return jsonify({'app': 'Flask Blog', 'posts': len(posts)})

@app.route('/posts')
def all_posts():
    return jsonify(posts)

@app.route('/health')
def health():
    return jsonify({'status': 'ok', 'app': 'blog'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)
EOF

cat > /var/www/blog/requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
EOF

cd /var/www/blog
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# Systemd:
cat > /etc/systemd/system/blog.service << 'EOF'
[Unit]
Description=Flask Blog
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/blog
Environment=PATH=/var/www/blog/venv/bin
ExecStart=/var/www/blog/venv/bin/gunicorn --workers 2 --bind 127.0.0.1:5001 app:app
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

chown -R www-data:www-data /var/www/blog
systemctl daemon-reload
systemctl enable blog
systemctl start blog
```

---

### Task 3: FastAPI Service (45 min)

```bash
mkdir -p /var/www/api
cat > /var/www/api/main.py << 'EOF'
from fastapi import FastAPI
import os, datetime

app = FastAPI(title="My Admin API", version="1.0")

@app.get("/")
async def root():
    return {'app': 'FastAPI', 'time': str(datetime.datetime.now())}

@app.get("/health")
async def health():
    return {'status': 'ok', 'app': 'api'}

@app.get("/system")
async def system_info():
    import subprocess
    disk = subprocess.run(['df', '-h', '/'], capture_output=True, text=True).stdout.split('\n')[1]
    mem = subprocess.run(['free', '-h'], capture_output=True, text=True).stdout.split('\n')[1]
    return {'disk': disk.split(), 'memory': mem.split()}
EOF

cat > /var/www/api/requirements.txt << 'EOF'
fastapi==0.100.0
uvicorn[standard]==0.22.0
gunicorn==20.1.0
EOF

cd /var/www/api
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt

cat > /etc/systemd/system/api.service << 'EOF'
[Unit]
Description=FastAPI Service
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/api
Environment=PATH=/var/www/api/venv/bin
ExecStart=/var/www/api/venv/bin/gunicorn \
    main:app -k uvicorn.workers.UvicornWorker \
    --workers 2 --bind 127.0.0.1:8001
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

chown -R www-data:www-data /var/www/api
systemctl daemon-reload
systemctl enable api
systemctl start api
```

---

### Task 4: Nginx Multi-Site Config (30 min)

```bash
cat > /etc/nginx/sites-available/challenge << 'EOF'
# Blog:
server {
    listen 80;
    server_name blog.yourdomain.com;
    location / {
        proxy_pass http://127.0.0.1:5001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    location /health { proxy_pass http://127.0.0.1:5001/health; }
}

# API:
server {
    listen 80;
    server_name api.yourdomain.com;
    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header Host $host;
    }
}

# Portfolio (static):
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    root /var/www/html;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}
EOF

ln -sf /etc/nginx/sites-available/challenge /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

---

### Task 5: Monitoring Dashboard (30 min)

```bash
cat > ~/monitor_all.sh << 'SCRIPTEOF'
#!/bin/bash
while true; do
    clear
    echo "╔══════════════════════════════════════╗"
    echo "║     🐧 LINUX SERVER DASHBOARD         ║"
    echo "╠══════════════════════════════════════╣"
    printf "║ Time: %-30s ║\n" "$(date '+%H:%M:%S %d/%m/%Y')"
    printf "║ Uptime: %-29s ║\n" "$(uptime -p)"
    echo "╠══════════════════════════════════════╣"
    echo "║ SERVICES                              ║"
    for svc in nginx blog api; do
        if systemctl is-active --quiet $svc 2>/dev/null; then
            printf "║   ✓ %-33s ║\n" "$svc: RUNNING"
        else
            printf "║   ✗ %-33s ║\n" "$svc: STOPPED"
        fi
    done
    echo "╠══════════════════════════════════════╣"
    echo "║ RESOURCES                             ║"
    DISK=$(df -h / | awk 'NR==2{print $3"/"$2" ("$5")"}')
    MEM=$(free -h | awk '/Mem/{print $3"/"$2}')
    LOAD=$(uptime | awk -F'load average:' '{gsub(/ /,"",$2); print $2}')
    printf "║   Disk: %-29s ║\n" "$DISK"
    printf "║   RAM:  %-29s ║\n" "$MEM"
    printf "║   Load: %-29s ║\n" "$LOAD"
    echo "╚══════════════════════════════════════╝"
    echo "(Ctrl+C to stop)"
    sleep 3
done
SCRIPTEOF

chmod +x ~/monitor_all.sh
echo "Monitor script ready: ~/monitor_all.sh"
```

---

### Task 6: Health Check & Alert (Bonus)

```bash
cat > ~/health_alert.sh << 'SCRIPTEOF'
#!/bin/bash
SERVICES=("nginx" "blog" "api")
LOG="/var/log/health_check.log"
FAILED=()

check_service() {
    systemctl is-active --quiet "$1" 2>/dev/null && return 0
    systemctl start "$1" 2>/dev/null
    sleep 2
    systemctl is-active --quiet "$1" 2>/dev/null && return 0
    return 1
}

for svc in "${SERVICES[@]}"; do
    if ! check_service "$svc"; then
        FAILED+=("$svc")
        echo "[$(date)] CRITICAL: $svc is DOWN and could not restart!" >> $LOG
    fi
done

if [ ${#FAILED[@]} -gt 0 ]; then
    echo "ALERT: Services down: ${FAILED[*]}" >> $LOG
    # Add email/Slack notification here
else
    echo "[$(date)] All services healthy" >> $LOG
fi
SCRIPTEOF

chmod +x ~/health_alert.sh

# Cron mein add karo:
echo "Add to cron: */5 * * * * /root/health_alert.sh"
```

---

## ✅ Final Challenge Checklist

- [ ] System update + UFW + fail2ban complete
- [ ] Flask Blog running on port 5001
- [ ] FastAPI Service running on port 8001  
- [ ] Nginx serving multiple virtual hosts
- [ ] Both apps respond to /health endpoint
- [ ] Systemd services enable hain (boot pe start)
- [ ] Monitoring dashboard kaam karta hai
- [ ] Health alert script cron mein hai
- [ ] Nginx logs aa rahe hain

## 🏆 Congratulations!

**Agar ye sab complete kar liya toh tum ek real Linux Server Administrator ho!**

```
Skills Mastered:
✓ Linux System Administration
✓ Nginx Multi-Site Configuration
✓ Python App Deployment (Flask + FastAPI)
✓ Systemd Service Management
✓ Server Security (UFW + fail2ban)
✓ Process Monitoring
✓ Automated Health Checks
✓ Production Server Operations
```

**Now you're ready for real-world DevOps! 🚀**
