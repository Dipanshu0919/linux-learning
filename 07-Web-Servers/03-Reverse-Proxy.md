# 🔄 Lesson 3: Reverse Proxy — Nginx as Middleware

---

## 📚 Learning Objectives

- Reverse proxy kya hai
- Nginx se Flask/FastAPI ko proxy karna
- Load balancing
- Proxy headers

---

## 🔄 Reverse Proxy kya hai?

```
WITHOUT Reverse Proxy:
Internet → Flask App (port 5000)
Problem: Flask directly internet se connected!

WITH Reverse Proxy (Nginx):
Internet (port 80/443) → Nginx → Flask (port 5000, localhost only)
Benefits:
  - Flask localhost pe (safe!)
  - Nginx SSL handle karta hai
  - Nginx static files serve karta hai
  - Multiple apps one server pe
  - Load balancing
```

---

## ⚡ Basic Reverse Proxy Config

```bash
sudo nano /etc/nginx/sites-available/flaskapp
```

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeouts:
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

---

## 🚀 Complete Flask Deployment with Nginx

```bash
# 1. Flask app:
mkdir -p /var/www/flaskapp
cat > /var/www/flaskapp/app.py << 'EOF'
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "<h1>Flask behind Nginx!</h1>"

@app.route('/api/hello')
def hello():
    return {"message": "Hello from Flask API!"}
EOF

# 2. Venv + Gunicorn:
cd /var/www/flaskapp
python3 -m venv venv
source venv/bin/activate
pip install flask gunicorn

# 3. Systemd service:
sudo tee /etc/systemd/system/flaskapp.service << 'EOF'
[Unit]
Description=Flask App
After=network.target

[Service]
User=codespace
WorkingDirectory=/var/www/flaskapp
Environment=PATH=/var/www/flaskapp/venv/bin
ExecStart=/var/www/flaskapp/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:5000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
EOF

# 4. Nginx config:
sudo tee /etc/nginx/sites-available/flaskapp << 'EOF'
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /static {
        alias /var/www/flaskapp/static;
        expires 30d;
    }
}
EOF

sudo ln -sf /etc/nginx/sites-available/flaskapp /etc/nginx/sites-enabled/
sudo rm -f /etc/nginx/sites-enabled/default
sudo nginx -t && sudo systemctl reload nginx
```

---

## ⚖️ Load Balancing

```nginx
upstream flask_servers {
    server 127.0.0.1:5000 weight=3;    # 3 requests
    server 127.0.0.1:5001 weight=1;    # 1 request
    server 127.0.0.1:5002;             # weight=1 default
}

server {
    listen 80;
    location / {
        proxy_pass http://flask_servers;
    }
}
```

---

## 🏋️ Practice (Codespaces)

```bash
sudo service nginx start 2>/dev/null || sudo systemctl start nginx

# Simple test:
echo "upstream backend { server 127.0.0.1:8080; }" | sudo tee /tmp/test.conf
echo "Valid concept tested!"

# Test reverse proxy:
python3 -m http.server 8080 &
SERVER_PID=$!
sleep 1
curl http://localhost:8080
kill $SERVER_PID
```

---

## 📖 Summary

```
proxy_pass http://127.0.0.1:5000   = Proxy to Flask
proxy_set_header Host              = Host header forward
upstream { server addr; }          = Load balancer group
proxy_pass http://upstream_name    = Use load balancer
```

**Next:** `07-Web-Servers/05-HTTPS-SSL.md` → HTTPS setup karo! 🔒
