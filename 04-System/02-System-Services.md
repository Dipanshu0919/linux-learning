# 🛠️ Lesson 2: System Services — systemctl Master Class

---

## 📚 Learning Objectives

- Linux services kya hote hain
- systemctl se services manage karna
- Custom service banana
- Service logs dekhna
- Boot pe auto-start configure karna

---

## 🏗️ Services kya hain?

**Services (Daemons) = Background mein hamesha chalte rehne wale programs**

```
Web Server    → nginx, apache2
Database      → postgresql, mysql
SSH Server    → sshd
Mail Server   → postfix
Your App      → flask_app.service (custom!)
```

### Systemd — Service Manager

Linux mein **systemd** sab services manage karta hai. `systemctl` uska tool hai.

```
systemd (PID 1 = System ka pehla process!)
    |
    |-- nginx.service
    |-- sshd.service
    |-- postgresql.service
    +-- your_app.service
```

---

## ⚡ Basic systemctl Commands

```bash
# Service start karo:
sudo systemctl start nginx
sudo systemctl start postgresql

# Service stop karo:
sudo systemctl stop nginx

# Service restart karo (stop + start):
sudo systemctl restart nginx

# Service reload karo (config reload, stop nahi):
sudo systemctl reload nginx
sudo systemctl reload-or-restart nginx

# Service status dekho:
sudo systemctl status nginx
sudo systemctl status sshd

# Boot pe auto-start enable:
sudo systemctl enable nginx
sudo systemctl enable --now nginx  # Enable + start abhi

# Boot pe auto-start disable:
sudo systemctl disable nginx

# Service active hai ya nahi:
systemctl is-active nginx

# Service enabled hai ya nahi:
systemctl is-enabled nginx
```

### Status Output Samjhna:
```bash
sudo systemctl status nginx
```
```
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
     Active: active (running) since Mon 2026-06-07 10:00:00 UTC
       Docs: man:nginx(8)
    Process: 892 ExecStart=/usr/sbin/nginx
   Main PID: 892 (nginx)
      Tasks: 2 (limit: 4400)
     Memory: 5.6M
        CPU: 12ms
     CGroup: /system.slice/nginx.service
             ├─892 "nginx: master process /usr/sbin/nginx"
             └─893 "nginx: worker process"

Jun 07 10:00:00 server systemd[1]: Starting nginx...
Jun 07 10:00:00 server nginx[892]: nginx: the configuration file syntax is ok
Jun 07 10:00:00 server systemd[1]: Started nginx.
```

---

## 📋 All Services Dekho

```bash
# Sab active services:
systemctl list-units --type=service --state=active

# Sab services (active + inactive):
systemctl list-units --type=service --all

# Failed services:
systemctl list-units --type=service --state=failed

# Systemd tree:
systemctl list-dependencies
```

---

## 📝 Custom Service Banana — Flask App ko Service Banao!

### Step 1: Flask App banao

```bash
mkdir -p /home/codespace/myflaskapp
cat > /home/codespace/myflaskapp/app.py << 'EOF'
from flask import Flask
import datetime

app = Flask(__name__)

@app.route('/')
def home():
    return f"Hello! Server time: {datetime.datetime.now()}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF
```

### Step 2: Service File Banao

```bash
sudo nano /etc/systemd/system/myflaskapp.service
```

Content:
```ini
[Unit]
Description=My Flask Application
After=network.target

[Service]
Type=simple
User=codespace
WorkingDirectory=/home/codespace/myflaskapp
Environment=FLASK_ENV=production
ExecStart=/usr/bin/python3 /home/codespace/myflaskapp/app.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### Step 3: Service Register aur Start Karo

```bash
# Systemd ko naya service batao:
sudo systemctl daemon-reload

# Service enable karo (boot pe start):
sudo systemctl enable myflaskapp

# Service start karo:
sudo systemctl start myflaskapp

# Status check:
sudo systemctl status myflaskapp
```

### Step 4: Verify

```bash
curl http://localhost:5000
# Output: Hello! Server time: 2026-06-07 10:30:00

# Logs dekho:
journalctl -u myflaskapp -f
```

---

## 🔧 Gunicorn ke saath Service (Production!)

```bash
# Gunicorn install:
pip3 install gunicorn

# Service file:
sudo nano /etc/systemd/system/flaskprod.service
```

```ini
[Unit]
Description=Flask Production App (Gunicorn)
After=network.target

[Service]
Type=notify
User=codespace
Group=codespace
WorkingDirectory=/home/codespace/myflaskapp
Environment="PATH=/home/codespace/.local/bin:/usr/bin"
ExecStart=/home/codespace/.local/bin/gunicorn --workers 3 --bind 0.0.0.0:5000 app:app
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=5
PrivateTmp=true
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

---

## 🏋️ Practice

```bash
# Practice 1: Sab services list karo
systemctl list-units --type=service --state=active | head -20

# Practice 2: SSH service status dekho
sudo systemctl status sshd 2>/dev/null || echo "SSH not running (Codespaces mein normal hai)"

# Practice 3: Cron service check karo
sudo systemctl status cron 2>/dev/null

# Practice 4: Service enable/disable test
# (Kisi harmless service pe try karo)
systemctl is-enabled cron
```

---

## 📖 Summary

```
systemctl start svc    = Service shuru karo
systemctl stop svc     = Service band karo
systemctl restart svc  = Restart
systemctl reload svc   = Config reload (stop nahi)
systemctl status svc   = Status dekho
systemctl enable svc   = Boot pe auto-start
systemctl disable svc  = Auto-start band
journalctl -u svc      = Service logs
daemon-reload          = Naya/modified service register
```

---

**Next:** `04-System/03-Logs.md` → Log files master karo! 📋
