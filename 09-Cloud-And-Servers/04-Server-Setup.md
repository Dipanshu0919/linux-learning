# 🖥️ Lesson 4: Complete Server Setup — From Zero to Production

---

## 📚 Learning Objectives

- Fresh server pe complete production setup karna
- Automated setup script banana
- Multiple apps same server pe run karna
- Server maintenance routine

---

## 🚀 Complete Server Setup Script

```bash
# Ek script jo sab kuch setup kare!
# Use: bash server_setup.sh

cat > server_setup.sh << 'SCRIPTEOF'
#!/bin/bash
set -e
echo "=== Complete Server Setup ==="

# Variables:
DEPLOY_USER="deploy"
APP_DIR="/var/www"

# 1. System Update:
echo "1. Updating system..."
apt-get update && apt-get upgrade -y

# 2. Essential packages:
echo "2. Installing packages..."
apt-get install -y \
    python3 python3-pip python3-venv \
    nginx git curl wget \
    ufw fail2ban certbot python3-certbot-nginx \
    htop nano vim \
    postgresql postgresql-contrib \
    redis-server \
    supervisor

# 3. Docker:
echo "3. Installing Docker..."
curl -fsSL https://get.docker.com | sh
usermod -aG docker $DEPLOY_USER 2>/dev/null || true

# 4. Deploy user:
echo "4. Creating deploy user..."
if ! id "$DEPLOY_USER" &>/dev/null; then
    adduser --disabled-password --gecos "" $DEPLOY_USER
    usermod -aG sudo,docker,www-data $DEPLOY_USER
fi

# 5. SSH hardening:
echo "5. Hardening SSH..."
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
systemctl reload sshd

# 6. Firewall:
echo "6. Setting up firewall..."
ufw --force reset
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 80/tcp
ufw allow 443/tcp
ufw --force enable

# 7. fail2ban:
echo "7. Configuring fail2ban..."
cat > /etc/fail2ban/jail.local << 'EOF2'
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
EOF2
systemctl restart fail2ban

# 8. Nginx:
echo "8. Configuring Nginx..."
systemctl enable nginx
systemctl start nginx

# 9. Swap (RAM extra - good for small servers):
echo "9. Creating swap..."
if [ ! -f /swapfile ]; then
    fallocate -l 1G /swapfile
    chmod 600 /swapfile
    mkswap /swapfile
    swapon /swapfile
    echo '/swapfile none swap sw 0 0' >> /etc/fstab
fi

# 10. App directory:
mkdir -p $APP_DIR
chown $DEPLOY_USER:$DEPLOY_USER $APP_DIR

echo "=== Server Setup Complete! ==="
echo "Next steps:"
echo "1. SSH keys deploy user ke liye setup karo"
echo "2. App deploy karo: /var/www/"
echo "3. Nginx virtual host configure karo"
echo "4. SSL: sudo certbot --nginx -d yourdomain.com"
SCRIPTEOF

chmod +x server_setup.sh
echo "server_setup.sh ready!"
```

---

## 🔄 Multiple Apps Same Server pe

```nginx
# /etc/nginx/sites-available/all_apps

# App 1 - Flask Blog:
server {
    listen 80;
    server_name blog.mydomain.com;
    location / {
        proxy_pass http://127.0.0.1:5001;
        proxy_set_header Host $host;
    }
}

# App 2 - FastAPI Service:
server {
    listen 80;
    server_name api.mydomain.com;
    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header Host $host;
    }
}

# App 3 - Static Site:
server {
    listen 80;
    server_name portfolio.mydomain.com;
    root /var/www/portfolio;
    index index.html;
}
```

---

## 📊 Server Monitoring Script

```bash
cat > ~/server_health.sh << 'EOF'
#!/bin/bash
DATE=$(date '+%Y-%m-%d %H:%M:%S')
echo "=== Server Health: $DATE ==="

# Services:
for service in nginx gunicorn; do
    if pgrep -x "$service" > /dev/null; then
        echo "✓ $service: Running"
    else
        echo "✗ $service: DOWN!"
    fi
done

# Disk:
DISK=$(df -h / | awk 'NR==2 {print $5}' | tr -d '%')
if [ $DISK -gt 90 ]; then
    echo "⚠ Disk: ${DISK}% (CRITICAL!)"
elif [ $DISK -gt 80 ]; then
    echo "⚠ Disk: ${DISK}% (Warning)"
else
    echo "✓ Disk: ${DISK}%"
fi

# Memory:
FREE_MEM=$(free | awk '/Mem/ {printf "%.0f", ($4/$2)*100}')
echo "✓ Free Memory: ${FREE_MEM}%"

# Load:
LOAD=$(uptime | awk -F'load average:' '{print $2}' | awk -F',' '{print $1}' | tr -d ' ')
echo "✓ Load: $LOAD"

echo "=== End ==="
EOF
chmod +x ~/server_health.sh
```

---

## 📖 Summary

```
server_setup.sh = Complete automated setup
fail2ban        = Brute force protection
/swapfile       = Extra RAM from disk
Multiple apps   = Different Nginx server blocks
Supervisor      = Process manager alternative
Regular backups = Always!
```

**Next:** `09-Cloud-And-Servers/Exercises.md` → Final exercises!
