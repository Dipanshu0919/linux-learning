# 🔐 Project 4: SSH & Complete Server Setup

**Difficulty:** Intermediate | **Time:** 3-4 hours | **Module:** 05, 09

---

## 🎯 Project Goal

SSH keys properly setup karo, SSH hardening karo, aur ek secure server environment configure karo.

---

## 📋 What You Will Learn

- Multiple SSH key pairs manage karna
- SSH config advanced options
- Server hardening checklist
- fail2ban configure karna
- Automated backup script

---

## 🔑 Part 1: SSH Key Management

```bash
# 3 alag-alag SSH keys banao alag purposes ke liye:

# 1. GitHub ke liye:
ssh-keygen -t ed25519 -C "github_$(date +%Y)" \
    -f ~/.ssh/github_key -N ""

# 2. Production server ke liye:
ssh-keygen -t ed25519 -C "production_server_$(date +%Y)" \
    -f ~/.ssh/production_key -N ""

# 3. Staging server ke liye:
ssh-keygen -t rsa -b 4096 -C "staging_$(date +%Y)" \
    -f ~/.ssh/staging_key -N ""

# Keys dekho:
ls -la ~/.ssh/
echo "Key fingerprints:"
for key in ~/.ssh/*.pub; do
    echo "  $(ssh-keygen -lf $key)"
done
```

---

## ⚙️ Part 2: SSH Config File

```bash
# Complete SSH config:
cat > ~/.ssh/config << 'EOF'
# ====================================
# SSH Configuration
# Generated: $(date)
# ====================================

# Default settings for all hosts:
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    ConnectTimeout 15
    AddKeysToAgent yes

# GitHub:
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_key
    IdentitiesOnly yes

# Production Server:
Host prod
    HostName YOUR_PROD_SERVER_IP
    User deploy
    Port 22
    IdentityFile ~/.ssh/production_key
    IdentitiesOnly yes
    StrictHostKeyChecking yes

# Staging Server:
Host staging
    HostName YOUR_STAGING_IP
    User deploy
    Port 2222
    IdentityFile ~/.ssh/staging_key
    ForwardAgent yes

# Jump host (bastion) example:
Host internal-server
    HostName 10.0.0.5
    User ubuntu
    ProxyJump bastion.company.com
    IdentityFile ~/.ssh/production_key
EOF

chmod 600 ~/.ssh/config
cat ~/.ssh/config
```

---

## 🛡️ Part 3: Server Hardening Script

```bash
cat > ~/server_hardening.sh << 'SCRIPTEOF'
#!/bin/bash
# Server Hardening Script
# Run as root on fresh Ubuntu 22.04

set -e
LOGFILE="/var/log/hardening.log"

log() { echo "[$(date)] $1" | tee -a $LOGFILE; }

log "=== Starting Server Hardening ==="

# 1. System Update:
log "1. System update..."
apt-get update && apt-get upgrade -y

# 2. fail2ban:
log "2. Installing fail2ban..."
apt-get install -y fail2ban

cat > /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime  = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true
port    = ssh
logpath = /var/log/auth.log
maxretry = 3

[nginx-http-auth]
enabled  = true
filter   = nginx-http-auth
logpath  = /var/log/nginx/error.log

[nginx-limit-req]
enabled  = true
filter   = nginx-limit-req
logpath  = /var/log/nginx/error.log
maxretry = 10
EOF

systemctl enable fail2ban
systemctl restart fail2ban

# 3. UFW:
log "3. Configuring firewall..."
ufw --force reset
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp comment 'SSH'
ufw allow 80/tcp comment 'HTTP'
ufw allow 443/tcp comment 'HTTPS'
ufw --force enable

# 4. SSH Hardening:
log "4. Hardening SSH..."
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

cat > /etc/ssh/sshd_config.d/hardening.conf << 'EOF'
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
LoginGraceTime 60
X11Forwarding no
AllowTcpForwarding no
PrintLastLog yes
Banner /etc/issue.net
EOF

echo "Authorized access only. All activity is logged." > /etc/issue.net

systemctl reload sshd

# 5. Swap:
log "5. Creating swap..."
if [ ! -f /swapfile ]; then
    fallocate -l 2G /swapfile
    chmod 600 /swapfile
    mkswap /swapfile
    swapon /swapfile
    echo '/swapfile none swap sw 0 0' >> /etc/fstab
fi

# 6. Kernel Security:
log "6. Kernel hardening..."
cat >> /etc/sysctl.conf << 'EOF'
net.ipv4.tcp_syncookies = 1
net.ipv4.ip_forward = 0
net.ipv4.conf.all.rp_filter = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
EOF
sysctl -p

log "=== Hardening Complete! ==="
log "Summary:"
log "  fail2ban: active"
log "  UFW: active (22, 80, 443 allowed)"
log "  SSH: password auth disabled"
log "  Swap: 2GB"
SCRIPTEOF

chmod +x ~/server_hardening.sh
echo "Hardening script ready!"
```

---

## 📦 Part 4: Automated Backup Script

```bash
cat > ~/backup.sh << 'SCRIPTEOF'
#!/bin/bash
# Automated Backup Script

BACKUP_DIR="/var/backups/myapp"
APP_DIR="/var/www/myapp"
DB_NAME="myapp_db"
DB_USER="myapp_user"
S3_BUCKET="my-backup-bucket"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=7

mkdir -p $BACKUP_DIR

# 1. App files backup:
tar -czf "$BACKUP_DIR/app_${DATE}.tar.gz" \
    --exclude="$APP_DIR/venv" \
    --exclude="$APP_DIR/__pycache__" \
    --exclude="$APP_DIR/*.pyc" \
    $APP_DIR
echo "✓ App backup: app_${DATE}.tar.gz"

# 2. Database backup:
if command -v pg_dump &>/dev/null; then
    pg_dump -U $DB_USER $DB_NAME | \
        gzip > "$BACKUP_DIR/db_${DATE}.sql.gz"
    echo "✓ DB backup: db_${DATE}.sql.gz"
fi

# 3. S3 upload (agar AWS CLI configured hai):
if command -v aws &>/dev/null; then
    aws s3 sync $BACKUP_DIR s3://$S3_BUCKET/backups/
    echo "✓ S3 sync complete"
fi

# 4. Old backups delete:
find $BACKUP_DIR -name "*.tar.gz" -mtime +$RETENTION_DAYS -delete
find $BACKUP_DIR -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete
echo "✓ Old backups cleaned (>${RETENTION_DAYS} days)"

echo "=== Backup Complete: $DATE ==="
ls -lh $BACKUP_DIR/ 2>/dev/null || echo "(Dry run - backup dir may not exist)"
SCRIPTEOF

chmod +x ~/backup.sh

# Test dry run:
echo "Backup script created!"
cat ~/backup.sh | head -10
```

---

## ✅ Success Criteria

- [ ] 3 alag SSH keys generate kiye hain
- [ ] ~/.ssh/config properly configure kiya hai
- [ ] SSH config file 600 permissions hain
- [ ] Server hardening script ready hai
- [ ] fail2ban configuration likhna aata hai
- [ ] UFW rules samajh aate hain
- [ ] Backup script complete hai

**Project 5: Mini DevOps Pipeline! ⚙️**
