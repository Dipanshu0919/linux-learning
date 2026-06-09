# 📋 Lesson 3: Logs — System ki Diary

---

## 📚 Learning Objectives

- Linux log system samjhna
- Important log files kahan hain
- journalctl use karna
- Log rotation samjhna
- Python app ke logs manage karna

---

## 📚 Log Files kahan hain?

```
/var/log/
    |-- syslog         = General system messages
    |-- auth.log       = Login/SSH attempts, sudo usage
    |-- kern.log       = Kernel messages
    |-- dmesg          = Boot messages, hardware
    |-- apt/           = Package manager logs
    |-- nginx/         = Nginx access + error logs
    |   |-- access.log = Every HTTP request
    |   +-- error.log  = Nginx errors
    |-- mysql/         = MySQL database logs
    |-- postgresql/    = PostgreSQL logs
    +-- journal/       = Systemd journal (binary format)
```

---

## 📖 Log Files Read Karna

```bash
# Syslog (general system messages):
sudo tail -f /var/log/syslog
sudo cat /var/log/syslog | tail -50

# Auth log (security!):
sudo tail -f /var/log/auth.log
sudo grep "Failed password" /var/log/auth.log   # Failed SSH attempts!
sudo grep "Accepted" /var/log/auth.log           # Successful logins

# Kernel messages:
dmesg                              # Boot messages
dmesg | tail -20                   # Recent kernel messages
dmesg | grep -i "error\|warning"   # Errors aur warnings

# Nginx logs:
sudo tail -f /var/log/nginx/access.log   # Live web traffic
sudo tail -f /var/log/nginx/error.log    # Nginx errors
```

---

## 🔍 `journalctl` — systemd Journal

Modern Linux systems mein `journalctl` use karo!

```bash
# Sab logs:
journalctl

# Last 50 lines:
journalctl -n 50

# Live monitoring:
journalctl -f

# Specific service ke logs:
journalctl -u nginx
journalctl -u myflaskapp
journalctl -u sshd -f    # SSH live logs

# Time range:
journalctl --since "2026-06-07 09:00:00"
journalctl --since "1 hour ago"
journalctl --until "2026-06-07 10:00:00"
journalctl --since "yesterday"

# Priority filter:
journalctl -p err         # Sirf errors
journalctl -p warning     # Warnings aur above

# Boot se:
journalctl -b             # Current boot
journalctl -b -1          # Previous boot
journalctl --list-boots   # All boots

# Kernel messages:
journalctl -k
```

---

## 🐍 Python App Logging

### logging module use karo:

```python
import logging
import logging.handlers

# Basic setup:
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('/var/log/myapp/app.log'),
        logging.StreamHandler()  # Terminal pe bhi
    ]
)

logger = logging.getLogger(__name__)

# Different levels:
logger.debug("Debug info (development only)")
logger.info("Normal operation info")
logger.warning("Something might be wrong!")
logger.error("Something went wrong!")
logger.critical("SYSTEM IS GOING DOWN!")
```

### Log Levels:
```
DEBUG    (10) = Detailed info, sirf development mein
INFO     (20) = Normal operations
WARNING  (30) = Something unexpected, but still working
ERROR    (40) = Error occurred
CRITICAL (50) = Serious error, might stop program
```

### Flask App Logging:
```python
import logging
from flask import Flask

app = Flask(__name__)

# Logging configure karo:
if not app.debug:
    file_handler = logging.FileHandler('app.log')
    file_handler.setLevel(logging.INFO)
    formatter = logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    )
    file_handler.setFormatter(formatter)
    app.logger.addHandler(file_handler)
    app.logger.setLevel(logging.INFO)
    app.logger.info('Flask application startup')

@app.route('/')
def home():
    app.logger.info('Home page accessed')
    return "Hello!"
```

---

## 🔄 Log Rotation

Logs agar rotate na ho toh hard disk full ho jaayega!

```bash
# Logrotate config dekho:
cat /etc/logrotate.conf
ls /etc/logrotate.d/

# Custom app ke liye logrotate:
sudo nano /etc/logrotate.d/myflaskapp
```

Content:
```
/var/log/myapp/app.log {
    daily              # Roz rotate karo
    rotate 7           # 7 din ki backup rakho
    compress           # Compress old logs
    delaycompress      # 1 din delay karo compression
    missingok          # Agar file nahi hai toh error mat
    notifempty         # Empty hone pe rotate mat karo
    postrotate
        systemctl reload myflaskapp
    endscript
}
```

---

## 🏋️ Practice

```bash
# Task 1: System logs dekhna
sudo journalctl -n 20
sudo journalctl --since "10 minutes ago"

# Task 2: Log file analysis
sudo grep "error\|warning" /var/log/syslog 2>/dev/null | head -10

# Task 3: Python logging practice
python3 << 'EOF'
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

logger.debug("System startup")
logger.info("Server started on port 5000")
logger.warning("Memory usage high: 85%")
logger.error("Database connection failed!")
logger.critical("Application crashed!")
EOF
```

---

## 📖 Summary

```
/var/log/syslog     = General system log
/var/log/auth.log   = Security/login log
/var/log/nginx/     = Web server logs
journalctl          = Systemd journal viewer
journalctl -f       = Live monitoring
journalctl -u svc   = Specific service logs
journalctl -p err   = Errors only
logrotate           = Automatic log rotation
Python logging      = logging module use karo
```

---

**Next:** `04-System/04-System-Monitoring.md` → System health monitor karo! 📊
