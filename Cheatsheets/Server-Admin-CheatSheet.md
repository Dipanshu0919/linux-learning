# 🖥️ Server Administration Cheatsheet

---

## 🚀 Service Management

```bash
sudo systemctl start service        # Start
sudo systemctl stop service         # Stop
sudo systemctl restart service      # Restart (downtime)
sudo systemctl reload service       # Reload config (no downtime)
sudo systemctl status service       # Status
sudo systemctl enable service       # Auto-start on boot
sudo systemctl disable service      # Disable auto-start
sudo systemctl is-active service    # Check active
sudo systemctl is-enabled service   # Check enabled
sudo systemctl list-units --type=service --state=active
sudo systemctl list-units --failed
sudo systemctl daemon-reload        # After editing .service file
```

---

## 📋 Logs

```bash
journalctl -f                       # Live system log
journalctl -u nginx -f              # Service log live
journalctl -u nginx -n 50           # Last 50 lines
journalctl --since "1 hour ago"     # Time filter
journalctl -p err                   # Errors only
journalctl -b                       # Current boot

# Nginx:
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
sudo grep "error" /var/log/nginx/error.log | tail -20

# Auth (security):
sudo grep "Failed password" /var/log/auth.log | tail -20
sudo grep "Accepted" /var/log/auth.log | tail -10
sudo fail2ban-client status sshd
```

---

## 📊 Resource Monitoring

```bash
# CPU:
uptime                              # Load average
top                                 # Live processes
htop                                # Better top
mpstat 1 5                          # Per-second stats
nproc                               # CPU count

# Memory:
free -h                             # RAM summary
cat /proc/meminfo | head -5         # Detailed

# Disk:
df -h                               # Disk usage
df -h /var/www                      # Specific path
du -sh /var/log                     # Dir size
du -sh * | sort -rh | head -10      # Biggest dirs
iostat 1 5                          # Disk I/O

# Top consumers:
ps aux --sort=-%cpu | head -10      # CPU hungry
ps aux --sort=-%mem | head -10      # Memory hungry
```

---

## 🌐 Nginx Quick Ops

```bash
sudo nginx -t                       # Test config
sudo nginx -T                       # Show full config
sudo systemctl reload nginx         # Reload (no downtime)
sudo systemctl restart nginx        # Full restart

# Sites:
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/mysite    # Disable

# Paths:
/etc/nginx/nginx.conf               # Main config
/etc/nginx/sites-available/        # Available sites
/etc/nginx/sites-enabled/          # Active sites
/var/log/nginx/access.log          # Access log
/var/log/nginx/error.log           # Error log
/var/www/html/                     # Default root
```

---

## 🔒 Security

```bash
# UFW Firewall:
sudo ufw status                     # Status
sudo ufw allow 22/tcp               # Allow SSH
sudo ufw allow 80/tcp               # Allow HTTP
sudo ufw allow 443/tcp              # Allow HTTPS
sudo ufw enable                     # Enable
sudo ufw status verbose             # Detailed

# fail2ban:
sudo fail2ban-client status         # Overall status
sudo fail2ban-client status sshd    # SSH jail
sudo fail2ban-client unban IP       # Unban IP
sudo tail -f /var/log/fail2ban.log  # Live log

# SSL:
sudo certbot --nginx -d domain.com  # Get certificate
sudo certbot renew --dry-run        # Test renewal
sudo certbot certificates           # List certs
ls /etc/letsencrypt/live/           # Cert files

# Audit:
sudo grep "sudo" /var/log/auth.log | tail -20  # Sudo usage
sudo last | head -20                # Login history
sudo lastb | head -10               # Failed logins
```

---

## 💾 Database Quick Ops

```bash
# PostgreSQL:
sudo -u postgres psql               # Connect as postgres
psql -U user -d dbname -h localhost # Connect with credentials
\l                                  # List databases
\c dbname                           # Connect to DB
\dt                                 # List tables
\q                                  # Quit

pg_dump -U user dbname > backup.sql          # Backup
pg_dump -U user dbname | gzip > backup.sql.gz # Compressed
psql -U user dbname < backup.sql             # Restore

# Redis:
redis-cli                           # Connect
redis-cli ping                      # Test (PONG)
redis-cli info                      # Info
redis-cli KEYS "*"                  # All keys
redis-cli FLUSHDB                   # Clear database (careful!)
```

---

## 🐍 Python App Operations

```bash
# Venv:
python3 -m venv venv                # Create
source venv/bin/activate            # Activate
deactivate                          # Deactivate
pip install -r requirements.txt     # Install deps
pip freeze > requirements.txt       # Save deps

# Gunicorn:
gunicorn app:app --workers 3 --bind 0.0.0.0:5000
gunicorn app:app --daemon --pid /tmp/gunicorn.pid
pkill gunicorn                      # Stop all

# FastAPI/Uvicorn:
uvicorn main:app --workers 4 --host 0.0.0.0 --port 8000
gunicorn main:app -k uvicorn.workers.UvicornWorker -w 4

# Process check:
ps aux | grep gunicorn
ps aux | grep uvicorn
pgrep -f "gunicorn app:app"
```

---

## 🔧 Useful One-Liners

```bash
# Find large files:
find / -size +100M -type f 2>/dev/null | sort -k5 -rn

# Delete old logs:
find /var/log -name "*.log" -mtime +30 -delete

# Count lines in all Python files:
find . -name "*.py" | xargs wc -l | tail -1

# Check if service is responding:
curl -sf http://localhost:5000/health && echo "OK" || echo "DOWN"

# Restart service if down:
systemctl is-active --quiet nginx || systemctl restart nginx

# Top 10 IP addresses in nginx log:
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# Disk usage by directory:
du -h --max-depth=1 /var/ | sort -rh | head -10

# All failed systemd services:
systemctl list-units --state=failed

# Watch a command every 2 seconds:
watch -n 2 "ss -tulpn | grep LISTEN"
```
