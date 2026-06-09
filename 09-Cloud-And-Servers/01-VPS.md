# ☁️ Lesson 1: VPS — Virtual Private Server

---

## 📚 Learning Objectives

- VPS kya hai
- VPS providers compare karna
- Fresh VPS setup karna
- Basic server hardening
- Python app deploy karna

---

## 🖥️ VPS kya hai?

```
VPS = Virtual Private Server
    = Cloud mein ek dedicated Linux server
    = 24/7 chalta rehta hai
    = Public IP address hota hai
    = Tumhara complete control!

Tumhara Dev PC:   Only when on
VPS Server:       24/7 on, internet pe!
```

---

## 💰 Popular VPS Providers

```
Provider       Cheapest Plan    Features
DigitalOcean   $4/mo (512MB)   Best for beginners!
Linode/Akamai  $5/mo (1GB)     Great support
Vultr          $2.50/mo        Very affordable
AWS EC2        Free tier (1yr)  Big ecosystem
Azure VM       Free tier (1yr)  Microsoft ecosystem
Hetzner        €3.79/mo        Best price EU
```

---

## 🚀 Fresh VPS Setup — Step by Step

### Step 1: VPS Create karo (DigitalOcean example)

```
1. account.digitalocean.com pe register karo
2. Create Droplet → Ubuntu 22.04 → Basic plan
3. Add your SSH public key:
   cat ~/.ssh/id_ed25519.pub  (copy karo)
4. Create Droplet
5. Public IP note karo: 203.45.67.89
```

### Step 2: First Time Login

```bash
# SSH se connect karo:
ssh root@203.45.67.89

# Ya agar SSH key set kiya hai:
ssh -i ~/.ssh/id_ed25519 root@203.45.67.89
```

### Step 3: System Update karo

```bash
# Latest packages:
apt-get update
apt-get upgrade -y
apt-get autoremove -y
```

### Step 4: New Admin User Banao

```bash
# Root direct use mat karo!
adduser deploy
usermod -aG sudo deploy

# SSH key copy karo:
mkdir -p /home/deploy/.ssh
cp ~/.ssh/authorized_keys /home/deploy/.ssh/
chown -R deploy:deploy /home/deploy/.ssh
chmod 700 /home/deploy/.ssh
chmod 600 /home/deploy/.ssh/authorized_keys

# Test karo (new terminal mein):
# ssh deploy@203.45.67.89
```

### Step 5: SSH Hardening

```bash
# /etc/ssh/sshd_config edit karo:
nano /etc/ssh/sshd_config

# Change/add these:
Port 2222                    # Default port change karo
PermitRootLogin no           # Root login band
PasswordAuthentication no    # Sirf SSH keys
MaxAuthTries 3

# Restart SSH:
systemctl restart sshd

# Ab naye terminal se test karo:
# ssh -p 2222 deploy@203.45.67.89
```

### Step 6: Firewall Setup

```bash
# UFW:
ufw default deny incoming
ufw default allow outgoing
ufw allow 2222/tcp    # SSH (naya port!)
ufw allow 80/tcp      # HTTP
ufw allow 443/tcp     # HTTPS
ufw enable
ufw status
```

### Step 7: Essential Software Install

```bash
# Essential packages:
apt-get install -y \
    python3 \
    python3-pip \
    python3-venv \
    nginx \
    git \
    curl \
    htop \
    fail2ban \
    certbot \
    python3-certbot-nginx \
    ufw

# fail2ban (brute force protection):
systemctl enable fail2ban
systemctl start fail2ban
```

### Step 8: Python App Deploy

```bash
# Deploy user se (switch karo):
su - deploy

# Code clone ya upload:
git clone https://github.com/yourusername/myapp.git /var/www/myapp
# Ya rsync se:
rsync -avz --exclude='.env' ./ deploy@203.45.67.89:/var/www/myapp/

cd /var/www/myapp

# Venv:
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# .env create karo:
nano .env

# Systemd service create karo... (refer to 07-Web-Servers/06-Flask-Deployment.md)
```

---

## 📋 VPS Maintenance Commands

```bash
# System update:
sudo apt-get update && sudo apt-get upgrade -y

# Disk check:
df -h

# Memory check:
free -h

# Running services:
sudo systemctl list-units --type=service --state=active

# Who's connected:
who
last | head -10

# Suspicious login attempts:
sudo grep "Failed password" /var/log/auth.log | tail -20

# fail2ban status:
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

---

## 🔒 Basic Security Checklist

```
[✓] Root login disabled
[✓] SSH key authentication only
[✓] UFW firewall enabled
[✓] fail2ban installed
[✓] Non-default SSH port
[✓] Regular system updates
[✓] Strong passwords (agar password auth hai)
[✓] Regular backups
[✓] SSL certificate (HTTPS)
```

---

## 📖 Summary

```
VPS = Cloud Linux server (24/7 on)
First setup:
1. apt-get update && upgrade
2. New admin user (root avoid karo)
3. SSH hardening (no root, no password)
4. UFW firewall
5. fail2ban
6. Essential software
7. Deploy your app!

ssh -p PORT user@SERVER_IP = Connect
ufw allow PORT             = Firewall rule
systemctl restart sshd     = SSH restart
```

**Next:** `09-Cloud-And-Servers/02-Azure-Basics.md` → Azure cloud! ☁️
