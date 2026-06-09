# 🐧 Linux Master Cheatsheet — Ek Page pe Sab Kuch!

> Print karo ya bookmark karo — yahi thumhara daily reference card hai!

---

## 🗂️ FILES & NAVIGATION

```
pwd              ls -la             cd /path          cd ~       cd -
touch file       mkdir -p a/b/c     cp -r src/ dst/   mv old new
rm -rf folder/   cat file           less file          head -20   tail -f
grep -rn "txt"   find . -name "*.py" wc -l             diff a b
tar -cvzf a.gz f/ tar -xvzf a.gz    zip -r a.zip f/   unzip a.zip
```

---

## 🔐 PERMISSIONS

```
ls -la                     # Permissions dekho
chmod 755 script.sh        # Executable
chmod 644 file.txt         # Regular file
chmod 600 .env             # Private!
chown user:group file      # Owner change
sudo chown -R www-data /var/www/

r=4  w=2  x=1  →  7=rwx  6=rw-  5=r-x  4=r--  0=---
755 = rwxr-xr-x    644 = rw-r--r--    600 = rw-------
```

---

## ⚙️ PROCESSES & SERVICES

```
ps aux              ps aux | grep nginx    top / htop
kill -9 PID         pkill gunicorn         pgrep python3
python3 app.py &    jobs                   fg 1     nohup cmd &

systemctl start/stop/restart/reload nginx
systemctl status nginx      systemctl enable nginx
journalctl -u nginx -f      journalctl -p err
```

---

## 💾 SYSTEM INFO

```
uname -a    df -h    free -h    uptime    nproc
du -sh /var/log    du -sh * | sort -rh | head -10
watch -n 2 "df -h"    top -bn1 | head -5
```

---

## 🌐 NETWORK

```
ip a                              # Network interfaces
ss -tulpn                         # Open ports
ss -tulpn | grep :80              # Specific port
ping -c 4 google.com              # Connectivity
curl -s https://api.example.com   # HTTP GET
curl -X POST URL -H "Content-Type: application/json" -d '{...}'
dig +short google.com             # DNS lookup
ssh user@host                     # Remote connect
ssh -i ~/.ssh/key user@host       # With key
scp file.txt user@host:/path/     # Upload
rsync -avz ./ user@host:/path/    # Smart sync
```

---

## 🔒 SSH

```
ssh-keygen -t ed25519 -C "email"  # Generate key
cat ~/.ssh/id_ed25519.pub          # Your public key
ssh-copy-id user@host             # Copy key to server
~/.ssh/config:
  Host myserver
      HostName 192.168.1.100
      User deploy
      IdentityFile ~/.ssh/id_ed25519
```

---

## 🌐 NGINX

```
sudo nginx -t                      # Test config
sudo systemctl reload nginx        # Apply changes
/etc/nginx/sites-available/        # Virtual host files
/var/log/nginx/access.log          # Access log

server {
    listen 80;
    server_name myapp.com;
    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

## 🐍 PYTHON + VENV

```
python3 -m venv venv           # Create venv
source venv/bin/activate       # Activate
deactivate                     # Deactivate
pip install -r requirements.txt
pip freeze > requirements.txt

gunicorn app:app --workers 3 --bind 0.0.0.0:5000
uvicorn main:app --workers 4 --host 0.0.0.0 --port 8000
gunicorn main:app -k uvicorn.workers.UvicornWorker -w 4
```

---

## 🐳 DOCKER

```
docker build -t myapp .              # Build image
docker run -d -p 5000:5000 myapp     # Run container
docker run -d --name web -p 8080:80 nginx
docker exec -it container bash       # Shell access
docker logs -f container             # Live logs
docker ps / docker ps -a             # Containers
docker stop/rm container             # Stop/delete
docker images / docker rmi image     # Images

docker-compose up -d                 # Start all
docker-compose down                  # Stop all
docker-compose logs -f web           # Service logs
docker-compose exec web bash         # Service shell
```

---

## 🌿 GIT

```
git init / git clone URL
git add . / git commit -m "msg" / git push origin main
git pull origin main / git fetch origin
git branch feature / git checkout feature / git checkout -b new
git merge feature / git rebase main
git stash / git stash pop
git log --oneline --graph / git diff
```

---

## 🔧 UFW FIREWALL

```
sudo ufw status           # Status check
sudo ufw allow 22/tcp     # SSH
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS
sudo ufw deny 3306/tcp    # Deny MySQL from outside
sudo ufw enable           # Enable firewall
```

---

## 📋 SYSTEMD SERVICE TEMPLATE

```ini
[Unit]
Description=My Flask App
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/myapp
Environment=PATH=/var/www/myapp/venv/bin
ExecStart=/var/www/myapp/venv/bin/gunicorn \
    --workers 3 --bind 127.0.0.1:5000 app:app
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

---

## 🔑 IMPORTANT FILE LOCATIONS

```
/etc/nginx/sites-available/   ← Nginx configs
/etc/systemd/system/          ← Service files
/var/www/                     ← Web apps
/var/log/nginx/               ← Nginx logs
/etc/letsencrypt/live/        ← SSL certificates
/etc/hosts                    ← Local DNS
/etc/ssh/sshd_config          ← SSH server config
~/.ssh/config                 ← SSH client config
~/.ssh/authorized_keys        ← Allowed keys
~/.bashrc                     ← Bash config
```

---

## 🚨 EMERGENCY COMMANDS

```bash
# Service down?
sudo systemctl status myapp
journalctl -u myapp -n 50
sudo systemctl restart myapp

# Port blocked?
ss -tulpn | grep :80
sudo ufw status

# Disk full?
df -h
du -sh /var/log/* | sort -rh | head -5
sudo journalctl --vacuum-size=500M   # Clear old logs

# Process eating CPU?
ps aux --sort=-%cpu | head -10
kill -9 PID

# Permission denied?
ls -la /path/to/file
sudo chown -R $USER /path/to/directory
```

---

*"The best Linux command is the one that solves your problem right now!"* 🐧
