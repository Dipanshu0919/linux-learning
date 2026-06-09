# 🚀 Lesson 6: Flask Deployment — Production pe Laao!

---

## 📚 Learning Objectives

- Flask app production-ready banana
- Gunicorn se production server run karna
- Nginx ke peeche Flask deploy karna
- Systemd service setup
- Logs aur monitoring

---

## 🏗️ Production Stack

```
Internet (Port 80/443)
      ↓
  Nginx (Reverse Proxy)
      ↓
  Gunicorn (WSGI Server, Port 5000)
      ↓
  Flask Application
      ↓
  Database (PostgreSQL/SQLite)
```

---

## 📦 Step 1: Flask App Production-Ready Banana

```bash
# Project structure:
mkdir -p /var/www/myflaskapp
cd /var/www/myflaskapp
mkdir -p {templates,static,logs}

# Main app:
cat > app.py << 'EOF'
from flask import Flask, jsonify, render_template
from dotenv import load_dotenv
import os
import logging

load_dotenv()

# Logging setup:
logging.basicConfig(
    filename='logs/app.log',
    level=logging.INFO,
    format='%(asctime)s %(levelname)s: %(message)s'
)
logger = logging.getLogger(__name__)

app = Flask(__name__)
app.config['SECRET_KEY'] = os.environ.get('SECRET_KEY', 'fallback-key')

@app.route('/')
def home():
    logger.info('Home page accessed')
    return render_template('index.html')

@app.route('/api/status')
def status():
    return jsonify({
        'status': 'running',
        'environment': os.environ.get('FLASK_ENV', 'production'),
        'version': '1.0.0'
    })

@app.errorhandler(404)
def not_found(error):
    return jsonify({'error': 'Not found'}), 404

@app.errorhandler(500)
def server_error(error):
    logger.error(f'Server error: {error}')
    return jsonify({'error': 'Internal server error'}), 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

# Templates:
cat > templates/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head><title>My Flask App</title></head>
<body>
    <h1>🐧 Flask on Linux!</h1>
    <p>Deployed with Gunicorn + Nginx</p>
</body>
</html>
EOF

# Requirements:
cat > requirements.txt << 'EOF'
flask==2.3.0
gunicorn==20.1.0
python-dotenv==0.21.0
EOF

# Environment:
cat > .env << 'EOF'
FLASK_ENV=production
SECRET_KEY=your-production-secret-key-here
EOF
```

---

## 🌿 Step 2: Virtual Environment Setup

```bash
cd /var/www/myflaskapp

# Venv:
python3 -m venv venv
source venv/bin/activate

# Install:
pip install -r requirements.txt

# Test karo dev mode mein:
python3 app.py
# Ctrl+C

# Gunicorn test:
gunicorn --workers 3 --bind 127.0.0.1:5000 app:app
# Ctrl+C
```

---

## ⚙️ Step 3: Systemd Service

```bash
sudo tee /etc/systemd/system/myflaskapp.service << 'EOF'
[Unit]
Description=My Flask Application (Gunicorn)
After=network.target
Wants=network-online.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myflaskapp
Environment="PATH=/var/www/myflaskapp/venv/bin"
EnvironmentFile=/var/www/myflaskapp/.env
ExecStart=/var/www/myflaskapp/venv/bin/gunicorn \
    --workers 3 \
    --worker-class sync \
    --bind 127.0.0.1:5000 \
    --timeout 120 \
    --access-logfile /var/www/myflaskapp/logs/gunicorn_access.log \
    --error-logfile /var/www/myflaskapp/logs/gunicorn_error.log \
    --log-level info \
    app:app
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=5
PrivateTmp=true
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Permissions:
sudo chown -R www-data:www-data /var/www/myflaskapp
sudo chmod -R 755 /var/www/myflaskapp
sudo chmod 600 /var/www/myflaskapp/.env

# Service enable aur start:
sudo systemctl daemon-reload
sudo systemctl enable myflaskapp
sudo systemctl start myflaskapp

# Status:
sudo systemctl status myflaskapp
```

---

## 🌐 Step 4: Nginx Configuration

```bash
sudo tee /etc/nginx/sites-available/myflaskapp << 'EOF'
upstream flask_app {
    server 127.0.0.1:5000;
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    # Security headers:
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    server_tokens off;

    # Request size limit:
    client_max_body_size 16M;

    # Static files (Nginx directly serve karta hai):
    location /static {
        alias /var/www/myflaskapp/static;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # Flask app (Gunicorn ko forward karo):
    location / {
        proxy_pass http://flask_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_redirect off;
        proxy_buffering off;

        proxy_connect_timeout 60;
        proxy_read_timeout 60;
    }

    # Error logs:
    access_log /var/log/nginx/myflaskapp_access.log;
    error_log /var/log/nginx/myflaskapp_error.log;
}
EOF

# Enable:
sudo ln -sf /etc/nginx/sites-available/myflaskapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🔒 Step 5: SSL Certificate (Production)

```bash
# Certbot se Let's Encrypt:
sudo apt-get install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Auto-renewal test:
sudo certbot renew --dry-run
```

---

## 📊 Step 6: Monitoring Setup

```bash
# Live logs:
sudo tail -f /var/www/myflaskapp/logs/app.log
sudo tail -f /var/www/myflaskapp/logs/gunicorn_access.log
sudo tail -f /var/log/nginx/myflaskapp_access.log

# Service status:
sudo systemctl status myflaskapp

# Gunicorn workers check:
ps aux | grep gunicorn

# Quick API test:
curl http://localhost/api/status
```

---

## 🔧 Deployment Script (Automation!)

```bash
cat > /var/www/myflaskapp/deploy.sh << 'EOF'
#!/bin/bash
set -e  # Error pe stop karo

PROJECT_DIR="/var/www/myflaskapp"
echo "=== Deploying Flask App ==="

cd $PROJECT_DIR

# Git se latest code:
git pull origin main

# Virtual env activate:
source venv/bin/activate

# Dependencies update:
pip install -r requirements.txt

# Tests run (optional):
# python3 -m pytest tests/

# Service restart:
sudo systemctl restart myflaskapp

# Health check:
sleep 3
if curl -s http://localhost:5000/api/status | grep -q "running"; then
    echo "✓ Deployment successful!"
else
    echo "✗ Health check failed! Rolling back..."
    git revert HEAD --no-edit
    sudo systemctl restart myflaskapp
    exit 1
fi

echo "=== Deployment Complete! ==="
EOF

chmod +x /var/www/myflaskapp/deploy.sh
```

---

## 🏋️ Practice (Codespaces mein)

```bash
# Mini deployment practice:
mkdir -p ~/flask_deploy_demo/{templates,logs}
cd ~/flask_deploy_demo

python3 -m venv venv && source venv/bin/activate
pip install flask gunicorn

cat > app.py << 'EOF'
from flask import Flask, jsonify
app = Flask(__name__)

@app.route('/')
def home():
    return "<h1>🚀 Deployed Flask App!</h1>"

@app.route('/api/health')
def health():
    return jsonify({'status': 'ok', 'message': 'Running!'})
EOF

# Gunicorn test:
gunicorn --workers 2 --bind 0.0.0.0:5001 app:app --daemon
sleep 2
curl http://localhost:5001/
curl http://localhost:5001/api/health
pkill gunicorn

deactivate && cd ~
```

---

## 🔧 Common Deployment Issues

```bash
# Issue: 502 Bad Gateway
# Cause: Flask app down hai
# Fix:
sudo systemctl status myflaskapp  # Status check
sudo systemctl restart myflaskapp
sudo journalctl -u myflaskapp -n 20  # Logs dekho

# Issue: 403 Forbidden
# Cause: File permissions galat hain
# Fix:
sudo chown -R www-data:www-data /var/www/myflaskapp
sudo chmod -R 755 /var/www/myflaskapp

# Issue: Port already in use
# Cause: Purana gunicorn chal raha hai
# Fix:
pkill gunicorn
sudo systemctl start myflaskapp

# Issue: Module not found
# Cause: Wrong venv ya package install nahi
# Fix:
source venv/bin/activate
pip install -r requirements.txt
```

---

## 📖 Summary

```
Flask Production Stack:
Nginx → Gunicorn → Flask

gunicorn app:app              = Production server
--workers 3                   = CPU cores based
--bind 127.0.0.1:5000        = Localhost only (Nginx ke peeche)
systemd service               = Auto-restart, boot-start
nginx proxy_pass              = Traffic forward karo
certbot --nginx               = SSL certificate
deploy.sh                     = Automated deployment script
```

---

**Next:** `07-Web-Servers/07-FastAPI-Deployment.md` → FastAPI deploy karo! ⚡
