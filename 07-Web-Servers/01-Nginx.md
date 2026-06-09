# 🌐 Lesson 1: Nginx — Fast Web Server

---

## 📚 Learning Objectives

- Nginx install aur configure karna
- Static files serve karna
- Virtual hosts setup karna
- Configuration structure samjhna
- Common configurations

---

## 🌐 Nginx kya hai?

**Nginx (Engine-X) = High-performance web server + Reverse proxy**

```
Internet → Nginx → Your Flask App
           |
           +-- Static files directly serve (fast!)
           +-- Multiple apps manage (reverse proxy)
           +-- Load balancing
           +-- SSL/HTTPS termination
```

### Nginx vs Apache:
```
Nginx:
    - Event-driven (ek process many connections)
    - Memory efficient
    - Best for high traffic
    - Static files serve karna → Nginx winner

Apache:
    - Thread-based
    - More modules
    - .htaccess support
    - Shared hosting pe common
```

---

## 📦 Nginx Install karna

```bash
# Install:
sudo apt-get update
sudo apt-get install nginx

# Status check:
sudo systemctl status nginx
sudo systemctl start nginx
sudo systemctl enable nginx  # Boot pe auto-start

# Version check:
nginx -v

# Test karo:
curl http://localhost
# Output: "Welcome to nginx!" default page
```

---

## 📁 Nginx Directory Structure

```bash
ls /etc/nginx/
```

```
/etc/nginx/
├── nginx.conf              ← Main config file
├── conf.d/                 ← Additional configs
│   └── default.conf        ← Default site
├── sites-available/        ← Available virtual hosts
│   └── default
├── sites-enabled/          ← Enabled virtual hosts (symlinks)
│   └── default → ../sites-available/default
├── snippets/               ← Reusable config snippets
│   └── fastcgi-php.conf
├── modules-available/
└── modules-enabled/
```

---

## 📝 Main Config File: nginx.conf

```bash
sudo cat /etc/nginx/nginx.conf
```

```nginx
user www-data;
worker_processes auto;          # CPU cores ke hisab se auto
pid /run/nginx.pid;

events {
    worker_connections 1024;    # Per worker max connections
    multi_accept on;
}

http {
    # Basic settings
    sendfile on;
    tcp_nopush on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Gzip compression
    gzip on;
    gzip_types text/plain application/json application/javascript text/css;

    # Virtual hosts include karo:
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

---

## 🌍 Virtual Host (Server Block) Config

### Static Website:

```bash
sudo nano /etc/nginx/sites-available/mywebsite
```

```nginx
server {
    listen 80;
    server_name mywebsite.com www.mywebsite.com;

    # Document root:
    root /var/www/mywebsite;
    index index.html index.htm;

    # Main location:
    location / {
        try_files $uri $uri/ =404;
    }

    # Static assets caching:
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # Error pages:
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```

```bash
# Enable karo (symlink):
sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/

# Config test karo:
sudo nginx -t
# Output: nginx: configuration file /etc/nginx/nginx.conf test is successful

# Reload karo:
sudo systemctl reload nginx
```

---

## 📁 Static Site Deploy karna

```bash
# Web root create karo:
sudo mkdir -p /var/www/mywebsite

# HTML file banao:
sudo tee /var/www/mywebsite/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>My Linux Site</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin-top: 100px; }
        h1 { color: #333; }
    </style>
</head>
<body>
    <h1>🐧 My First Nginx Site!</h1>
    <p>Deployed on Linux with Nginx</p>
    <p>Server is running smoothly!</p>
</body>
</html>
EOF

# Permissions:
sudo chown -R www-data:www-data /var/www/mywebsite
sudo chmod -R 755 /var/www/mywebsite

# Test:
curl http://localhost
```

---

## 🔧 Nginx Commands

```bash
# Config test:
sudo nginx -t
sudo nginx -T    # Complete config print (with includes)

# Start/Stop/Restart:
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx

# Reload config (no downtime!):
sudo systemctl reload nginx
# Ya:
sudo nginx -s reload

# Graceful shutdown:
sudo nginx -s quit

# Logs:
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

---

## 🔒 Basic Security Config

```bash
sudo nano /etc/nginx/sites-available/secure_site
```

```nginx
server {
    listen 80;
    server_name example.com;

    # Security headers:
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    add_header Referrer-Policy "strict-origin-when-cross-origin";

    # Hide Nginx version:
    server_tokens off;

    # Limit request size:
    client_max_body_size 10M;

    # Rate limiting:
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

    location / {
        limit_req zone=mylimit burst=20 nodelay;
        try_files $uri $uri/ =404;
    }

    # Deny hidden files:
    location ~ /\. {
        deny all;
    }
}
```

---

## 🏋️ Practice (Codespaces mein)

```bash
# Codespaces mein nginx install:
sudo apt-get install nginx -y

# Start:
sudo service nginx start  # Codespaces mein systemctl nahi, service use karo

# Test:
curl http://localhost
# Ya:
curl -I http://localhost   # Headers check

# Log dekho:
sudo tail -5 /var/log/nginx/access.log

# Config test:
sudo nginx -t

# Custom page create karo:
echo "<h1>My Custom Nginx Page!</h1>" | sudo tee /var/www/html/index.html
curl http://localhost
```

---

## 📖 Summary

```
sudo apt install nginx           = Install
sudo systemctl start nginx       = Start
sudo systemctl reload nginx      = Config reload (no downtime)
sudo nginx -t                    = Config test
/etc/nginx/nginx.conf            = Main config
/etc/nginx/sites-available/      = Available sites
/etc/nginx/sites-enabled/        = Active sites (symlinks)
/var/www/html/                   = Default web root
/var/log/nginx/access.log        = Access logs
/var/log/nginx/error.log         = Error logs
```

---

**Next:** `07-Web-Servers/03-Reverse-Proxy.md` → Flask ko Nginx se serve karo! 🔄
