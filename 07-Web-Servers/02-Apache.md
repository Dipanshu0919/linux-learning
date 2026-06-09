# 🪶 Lesson 2: Apache — The Classic Web Server

---

## 📚 Learning Objectives

- Apache install aur configure karna
- Virtual hosts setup karna
- .htaccess use karna
- Apache vs Nginx differences

---

## 🪶 Apache kya hai?

**Apache HTTP Server = Sabse purana aur popular web server**

```
Apache Use Cases:
- Shared hosting (cPanel etc.)
- .htaccess support chahiye ho
- PHP apps (WordPress, Drupal, etc.)
- CGI scripts
- Old school hosting
```

---

## 📦 Apache Install

```bash
# Install:
sudo apt-get install apache2

# Status:
sudo systemctl status apache2
sudo systemctl start apache2
sudo systemctl enable apache2

# Version:
apache2 -v

# Test:
curl http://localhost
# "Apache2 Ubuntu Default Page"
```

---

## 📁 Apache Directory Structure

```
/etc/apache2/
├── apache2.conf          ← Main config
├── ports.conf            ← Listen ports
├── sites-available/      ← Available virtual hosts
│   └── 000-default.conf  ← Default site
├── sites-enabled/        ← Active sites (symlinks)
│   └── 000-default.conf
├── mods-available/       ← Available modules
└── mods-enabled/         ← Active modules (symlinks)

/var/www/html/            ← Default document root
/var/log/apache2/         ← Logs
```

---

## 🌍 Virtual Host Config

```bash
sudo nano /etc/apache2/sites-available/mysite.conf
```

```apache
<VirtualHost *:80>
    ServerName mysite.com
    ServerAlias www.mysite.com
    DocumentRoot /var/www/mysite

    <Directory /var/www/mysite>
        Options Indexes FollowSymLinks
        AllowOverride All    # .htaccess allow karo
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/mysite_error.log
    CustomLog ${APACHE_LOG_DIR}/mysite_access.log combined
</VirtualHost>
```

```bash
# Enable site:
sudo a2ensite mysite.conf

# Disable site:
sudo a2dissite 000-default.conf

# Test config:
sudo apache2ctl configtest
# Syntax OK

# Reload:
sudo systemctl reload apache2
```

---

## 📄 .htaccess — Per-Directory Config

```bash
cat > /var/www/mysite/.htaccess << 'EOF'
# Redirect www to non-www:
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ http://%1/$1 [R=301,L]

# Python Flask WSGI (mod_wsgi ke saath):
# WSGIScriptAlias / /var/www/myapp/app.wsgi

# Custom error pages:
ErrorDocument 404 /404.html
ErrorDocument 500 /500.html

# Block direct access to .py files:
<Files "*.py">
    Require all denied
</Files>

# Gzip compression:
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/css application/javascript
</IfModule>
EOF
```

---

## 🔧 Apache Modules

```bash
# Available modules:
ls /etc/apache2/mods-available/ | head -20

# Enable module:
sudo a2enmod rewrite      # URL rewriting
sudo a2enmod ssl          # HTTPS
sudo a2enmod headers      # Custom headers
sudo a2enmod deflate      # Gzip compression
sudo a2enmod wsgi         # Python WSGI

# Disable module:
sudo a2dismod module_name

# Reload after changes:
sudo systemctl reload apache2
```

---

## 🐍 Python Flask on Apache (mod_wsgi)

```bash
# Install mod_wsgi:
sudo apt-get install libapache2-mod-wsgi-py3
sudo a2enmod wsgi

# WSGI file banao:
cat > /var/www/flaskapp/app.wsgi << 'EOF'
import sys
sys.path.insert(0, '/var/www/flaskapp')
from app import app as application
EOF

# Apache config:
sudo nano /etc/apache2/sites-available/flaskapp.conf
```

```apache
<VirtualHost *:80>
    ServerName myflask.com

    WSGIDaemonProcess flaskapp python-home=/var/www/flaskapp/venv
    WSGIProcessGroup flaskapp
    WSGIScriptAlias / /var/www/flaskapp/app.wsgi

    <Directory /var/www/flaskapp>
        Require all granted
    </Directory>
</VirtualHost>
```

---

## 📖 Apache vs Nginx Quick Ref

```
Feature          | Apache      | Nginx
-----------------|-------------|-------------
Architecture     | Thread-based| Event-based
Memory           | Higher      | Lower
Static files     | Good        | Excellent
Dynamic content  | mod_php etc | Needs proxy
.htaccess        | Yes!        | No (config file)
Config reload    | Restart req | Reload (no downtime)
Popularity       | Very high   | High (growing)
Shared hosting   | Common      | Less common
Best for         | Traditional | Modern apps
```

---

## 🏋️ Practice

```bash
# Install aur test:
sudo apt-get install apache2 -y
sudo service apache2 start 2>/dev/null || sudo systemctl start apache2
curl -s http://localhost | grep -i "apache\|html" | head -3

# Custom page:
echo "<h1>My Apache Page!</h1>" | sudo tee /var/www/html/index.html
curl http://localhost

# Logs check:
sudo tail -5 /var/log/apache2/access.log
```

---

## 📖 Summary

```
sudo apt install apache2    = Install
sudo a2ensite config.conf   = Enable site
sudo a2dissite config.conf  = Disable site
sudo a2enmod modulename     = Enable module
apache2ctl configtest       = Config test
sudo systemctl reload apache2 = Reload
/var/www/html/              = Default root
/etc/apache2/sites-available/ = Virtual hosts
.htaccess                   = Per-directory config
```

**Next:** `07-Web-Servers/03-Reverse-Proxy.md` → Nginx reverse proxy! 🔄
