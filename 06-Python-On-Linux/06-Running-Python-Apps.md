# 🚀 Lesson 6: Running Python Apps on Linux

---

## 📚 Learning Objectives

- Python scripts different ways se run karna
- Flask aur FastAPI apps run karna
- Gunicorn aur Uvicorn production servers
- Background processes aur systemd services
- Cron jobs se scheduled tasks

---

## 🐍 Python Script Run Karna

```bash
# Basic:
python3 app.py
python3 -m module_name

# With shebang (script executable banana):
# File ke pehle line mein:
#!/usr/bin/env python3

# Phir:
chmod +x app.py
./app.py        # Direct run!

# Module as script:
python3 -m http.server 8080   # Simple HTTP server
python3 -m json.tool          # JSON pretty print
python3 -m venv venv          # Virtual env
```

---

## 🌐 Flask Development Server

```bash
# app.py banao:
cat > ~/flask_run_demo/app.py << 'EOF'
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return '<h1>Flask Running on Linux!</h1>'

@app.route('/api/status')
def status():
    return jsonify({'status': 'running', 'message': 'Linux is awesome!'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
EOF

# Setup:
mkdir -p ~/flask_run_demo && cd ~/flask_run_demo
python3 -m venv venv && source venv/bin/activate
pip install flask

# Run karo:
python3 app.py
# Ya Flask CLI se:
FLASK_APP=app.py flask run
FLASK_APP=app.py FLASK_DEBUG=1 flask run --host=0.0.0.0 --port=5000
```

---

## 🦄 Gunicorn — Production Flask Server

```bash
# Install:
pip install gunicorn

# Basic run:
gunicorn app:app

# Full options:
gunicorn \
    --workers 3 \
    --bind 0.0.0.0:5000 \
    --timeout 120 \
    --access-logfile access.log \
    --error-logfile error.log \
    app:app

# Workers formula: (2 × CPU cores) + 1
# 2 cores: 5 workers
nproc    # CPU count check karo
gunicorn --workers $(( 2 * $(nproc) + 1 )) app:app

# Background mein:
gunicorn --workers 3 --bind 0.0.0.0:5000 --daemon app:app

# Config file se:
cat > gunicorn.conf.py << 'GEOF'
workers = 3
bind = "0.0.0.0:5000"
timeout = 120
accesslog = "access.log"
errorlog = "error.log"
loglevel = "info"
GEOF
gunicorn -c gunicorn.conf.py app:app
```

---

## ⚡ FastAPI aur Uvicorn

```bash
# Install:
pip install fastapi uvicorn

# fastapi_app.py:
cat > ~/flask_run_demo/fastapi_app.py << 'EOF'
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None

@app.get("/")
async def root():
    return {"message": "FastAPI on Linux!"}

@app.get("/api/items/{item_id}")
async def get_item(item_id: int):
    return {"item_id": item_id, "name": "Sample Item"}

@app.post("/api/items")
async def create_item(item: Item):
    return {"message": "Created!", "item": item}
EOF

# Development run:
uvicorn fastapi_app:app --reload --host 0.0.0.0 --port 8000

# Production run:
uvicorn fastapi_app:app --workers 4 --host 0.0.0.0 --port 8000
# Ya gunicorn ke saath:
gunicorn fastapi_app:app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

---

## ⏰ Cron Jobs — Scheduled Tasks

```bash
# Cron jobs dekhna/edit karna:
crontab -e     # Current user ke cron jobs edit
crontab -l     # List karo
crontab -r     # Remove all (careful!)

# System cron:
ls /etc/cron.d/
ls /etc/cron.daily/
ls /etc/cron.hourly/
```

### Cron Format:
```
* * * * * command
| | | | |
| | | | +-- Day of week (0-7, 0=Sunday)
| | | +---- Month (1-12)
| | +------ Day of month (1-31)
| +-------- Hour (0-23)
+---------- Minute (0-59)

Examples:
* * * * *          = Har minute
0 * * * *          = Har ghante ki shuru mein
0 2 * * *          = Roz raat 2 baje
0 2 * * 0          = Har Sunday raat 2 baje
*/15 * * * *       = Har 15 minute
0 0 1 * *          = Har mahine ki 1st ko midnight
0 8-18 * * 1-5     = Weekdays 8am-6pm har ghante
```

```bash
# Practical cron examples:
crontab -e

# Add these:
# Roz raat 2 baje backup:
0 2 * * * /home/codespace/backup.sh >> /var/log/backup.log 2>&1

# Har 15 min health check:
*/15 * * * * /home/codespace/health_check.py >> /var/log/health.log

# Har Sunday purge temp files:
0 3 * * 0 find /tmp -mtime +7 -delete
```

---

## 📊 Process as Background Service

```bash
# Nohup se (terminal close hone ke baad bhi chale):
nohup gunicorn app:app &
nohup python3 app.py > app.log 2>&1 &
echo $!    # PID print karo

# Screen se (terminal session save karo):
sudo apt-get install screen

screen -S myapp          # New screen session
# App run karo...
python3 app.py

# Ctrl+A, D = Detach (app chalti rehegi!)
screen -ls               # Sessions list
screen -r myapp          # Re-attach

# tmux (better screen):
sudo apt-get install tmux
tmux new -s myapp
# App run karo...
# Ctrl+B, D = Detach
tmux ls
tmux attach -t myapp
```

---

## 🏋️ Practice

```bash
# Complete Flask + Gunicorn demo:
mkdir ~/production_demo && cd ~/production_demo
python3 -m venv venv && source venv/bin/activate
pip install flask gunicorn

cat > app.py << 'EOF'
from flask import Flask, jsonify
import os, datetime

app = Flask(__name__)

@app.route('/')
def home():
    return jsonify({
        'message': 'Production Flask App!',
        'time': str(datetime.datetime.now()),
        'pid': os.getpid()
    })
EOF

# Dev server:
echo "Starting dev server (Ctrl+C to stop)..."
python3 app.py &
sleep 2
curl http://localhost:5000/
kill %1

# Gunicorn:
echo "Starting Gunicorn..."
gunicorn --workers 2 --bind 0.0.0.0:5001 app:app --daemon
sleep 2
curl http://localhost:5001/
pkill gunicorn

deactivate && cd ~
```

---

## 📖 Summary

```
python3 app.py              = Basic run
FLASK_APP=app.py flask run  = Flask CLI
gunicorn app:app            = Production Flask
uvicorn app:app --reload    = FastAPI dev
uvicorn app:app --workers 4 = Production FastAPI
nohup cmd &                 = Background (persistent)
crontab -e                  = Schedule tasks
screen / tmux               = Terminal sessions
```

**Next:** `06-Python-On-Linux/Exercises.md` → Practice karo! 💪
