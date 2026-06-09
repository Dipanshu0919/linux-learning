# 🔒 Lesson 5: HTTPS & SSL — Secure Your Site!

---

## 📚 Learning Objectives

- SSL/TLS kya hai
- Let's Encrypt se free certificate lena
- Certbot use karna
- HTTP to HTTPS redirect
- Self-signed certificates (testing)

---

## 🔒 SSL/TLS kya hai?

```
HTTP  = Hyper Text Transfer Protocol  (NO encryption)
HTTPS = HTTP + SSL/TLS encryption     (SECURE!)

Without HTTPS:
Browser → Plain text data → Internet → Server
Koi bhi traffic dekh sakta hai! (Passwords, credit cards!)

With HTTPS:
Browser → Encrypted data → Internet → Server
Data encrypted hai — intercepting karo toh bhi unreadable!
```

### SSL Certificate kya hota hai?
```
Certificate = Digital ID card for your website

Contains:
- Domain name: mysite.com
- Organization info
- Public key
- Expiry date
- Digital signature from CA (Certificate Authority)
```

---

## 🌿 Let's Encrypt — Free SSL Certificates!

**Let's Encrypt = Free, automated, open CA**

```bash
# Certbot install karo:
sudo apt-get install certbot python3-certbot-nginx

# Certificate lena (domain chahiye!):
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Interactive prompts:
# Email: your@email.com
# Terms: Y
# Share email: N (optional)
# Redirect HTTP to HTTPS: 2 (yes, recommended!)

# Auto-renewal test:
sudo certbot renew --dry-run

# Certificates dekho:
sudo certbot certificates
ls /etc/letsencrypt/live/yourdomain.com/
```

---

## 📁 Certificate Files

```
/etc/letsencrypt/live/yourdomain.com/
├── cert.pem       ← Certificate
├── chain.pem      ← Intermediate certificates
├── fullchain.pem  ← cert.pem + chain.pem (use this!)
└── privkey.pem    ← Private key (SECRET! 600 permissions)
```

---

## ⚙️ Nginx HTTPS Config

```nginx
# /etc/nginx/sites-available/mysite

# HTTP → HTTPS redirect:
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

# HTTPS server:
server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    # SSL Certificate (Let's Encrypt):
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    # SSL Settings:
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;

    # HSTS (tell browsers to always use HTTPS):
    add_header Strict-Transport-Security "max-age=31536000" always;

    # Security headers:
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```

---

## 🔑 Self-Signed Certificate (Testing ke liye)

```bash
# Production mein mat use karo! Sirf testing ke liye.

# Generate karo:
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/nginx-selfsigned.key \
    -out /etc/ssl/certs/nginx-selfsigned.crt

# Prompts fill karo:
# Country: IN
# State: Maharashtra
# City: Mumbai
# Organization: My Company
# OU: IT
# Common Name: localhost   ← Important!
# Email: admin@localhost

# Nginx config:
sudo nano /etc/nginx/sites-available/test-ssl
```

```nginx
server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

    location / {
        proxy_pass http://127.0.0.1:5000;
    }
}
```

---

## 🔄 Auto-Renewal Setup

```bash
# Certbot auto-renewal cron:
sudo crontab -l | grep certbot  # Check existing

# Manual add (usually certbot does it):
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet

# Systemd timer check (Ubuntu):
sudo systemctl status certbot.timer

# Test renewal:
sudo certbot renew --dry-run
```

---

## 🏋️ Practice

```bash
# Self-signed cert create karo (testing):
sudo apt-get install openssl -y
sudo mkdir -p /etc/ssl/practice

sudo openssl req -x509 -nodes -days 30 -newkey rsa:2048 \
    -keyout /etc/ssl/practice/test.key \
    -out /etc/ssl/practice/test.crt \
    -subj "/C=IN/ST=MH/L=Mumbai/O=Learning/CN=localhost"

# Files check karo:
ls -la /etc/ssl/practice/
sudo openssl x509 -in /etc/ssl/practice/test.crt -noout -text | head -20
```

---

## 📖 Summary

```
certbot --nginx -d domain    = Let's Encrypt cert
certbot renew --dry-run      = Test auto-renewal
/etc/letsencrypt/live/       = Certificates location
fullchain.pem                = Certificate chain (use this)
privkey.pem                  = Private key
ssl_certificate              = Nginx cert path
ssl_certificate_key          = Nginx key path
return 301 https://          = HTTP to HTTPS redirect
```

**Next:** `07-Web-Servers/06-Flask-Deployment.md` → Complete deployment! 🚀
