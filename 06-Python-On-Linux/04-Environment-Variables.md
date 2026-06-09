# 🔧 Lesson 4: Environment Variables — Config Management

---

## 📚 Learning Objectives

- Environment variables kya hain
- Variables set karna
- .env file use karna
- Python mein env vars access karna
- Secrets management best practices

---

## 🔧 Environment Variables kya hain?

**Env vars = Configuration values jo system environment mein store hote hain**

```
Code mein hardcode karna (BAD!):
    DATABASE_URL = "postgresql://user:password123@localhost/mydb"
    API_KEY = "sk-1234567890abcdef"
    # Problem: GitHub pe chali gayi! Sab dekh sakte hain!

Environment variables use karna (GOOD!):
    DATABASE_URL = os.environ.get("DATABASE_URL")
    API_KEY = os.environ.get("API_KEY")
    # Values environment se aati hain, code mein nahi hoti!
```

---

## ⚡ Basic Commands

```bash
# Sab env vars dekho:
env
printenv

# Specific var:
echo $HOME
echo $PATH
echo $USER
echo $SHELL

# Var set karo (current session only):
export MY_VAR="Hello Linux"
echo $MY_VAR

# Var unset karo:
unset MY_VAR
echo $MY_VAR    # Empty

# Check if set:
echo ${MY_VAR:-"default value"}  # Default if not set
```

---

## 🌍 Important System Env Vars

```bash
# Common system variables:
echo $HOME        # /home/codespace
echo $USER        # codespace
echo $PATH        # Executable paths
echo $SHELL       # /bin/bash
echo $PWD         # Current directory (same as pwd)
echo $HOSTNAME    # Computer name
echo $LANG        # Language setting
echo $EDITOR      # Default text editor

# PATH samjhna:
echo $PATH | tr ':' '\n'
# /home/codespace/.local/bin
# /usr/local/sbin
# /usr/local/bin
# /usr/sbin
# /usr/bin
# /sbin
# /bin
# (Jab command type karte ho, yahan dhundha jaata hai)
```

---

## 📁 .env File — Python projects ke liye

```bash
# .env file banao:
cat > .env << 'EOF'
# Application Settings
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=my-super-secret-key-change-in-production
DEBUG=True

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# API Keys
OPENAI_API_KEY=sk-your-key-here
SENDGRID_API_KEY=SG.your-key-here

# App Config
PORT=5000
HOST=0.0.0.0
ALLOWED_HOSTS=localhost,127.0.0.1
EOF

# IMPORTANT: .gitignore mein add karo!
echo ".env" >> .gitignore

# .env.example banao (template - secrets nahi):
cat > .env.example << 'EOF'
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=change-me-to-random-string
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
OPENAI_API_KEY=your-openai-key-here
PORT=5000
EOF
```

---

## 🐍 Python mein Environment Variables

```bash
# python-dotenv install karo:
pip install python-dotenv
```

```python
# config.py
import os
from dotenv import load_dotenv

# .env file load karo:
load_dotenv()

# Values access karo:
SECRET_KEY = os.environ.get('SECRET_KEY')
DATABASE_URL = os.environ.get('DATABASE_URL', 'sqlite:///default.db')  # Default value
DEBUG = os.environ.get('DEBUG', 'False').lower() == 'true'
PORT = int(os.environ.get('PORT', 5000))

# Flask app mein:
```

```python
# app.py
from flask import Flask
from dotenv import load_dotenv
import os

load_dotenv()

app = Flask(__name__)
app.config['SECRET_KEY'] = os.environ.get('SECRET_KEY', 'dev-secret')
app.config['DEBUG'] = os.environ.get('FLASK_DEBUG', 'False') == 'True'

DATABASE_URL = os.environ.get('DATABASE_URL')
if not DATABASE_URL:
    raise ValueError("DATABASE_URL environment variable is not set!")

@app.route('/')
def home():
    return f"Running in {os.environ.get('FLASK_ENV', 'unknown')} mode"

if __name__ == '__main__':
    app.run(
        host=os.environ.get('HOST', '0.0.0.0'),
        port=int(os.environ.get('PORT', 5000))
    )
```

---

## 🔒 Secrets Management Best Practices

```bash
# 1. NEVER .env ko git mein daalo:
git add .env       # NEVER DO THIS!
echo ".env" >> .gitignore  # Always!

# 2. Production mein system env vars use karo:
export DATABASE_URL="postgresql://user:pass@prod-db/mydb"

# 3. Systemd service mein:
# /etc/systemd/system/myapp.service
# [Service]
# Environment=DATABASE_URL=postgresql://...
# EnvironmentFile=/etc/myapp/production.env

# 4. Docker mein:
# docker run -e DATABASE_URL=postgresql://... myapp
# docker-compose.yml mein env_file: .env

# 5. Cloud mein:
# AWS: Parameter Store ya Secrets Manager
# Azure: Key Vault
# Heroku: Config Vars
```

---

## 🏋️ Practice

```bash
# 1. Custom env var set karo:
export MY_APP_NAME="Linux Learning App"
export MY_APP_VERSION="1.0.0"
export MY_DEBUG=true

# 2. Python se access karo:
python3 -c "
import os
print('App:', os.environ.get('MY_APP_NAME', 'Not set'))
print('Version:', os.environ.get('MY_APP_VERSION', 'Not set'))
print('Debug:', os.environ.get('MY_DEBUG', 'False'))
"

# 3. .env file workflow:
mkdir ~/env_practice && cd ~/env_practice
python3 -m venv venv
source venv/bin/activate
pip install python-dotenv

cat > .env << 'EOF2'
APP_NAME=My Linux App
APP_VERSION=1.0
SECRET=super_secret_123
DATABASE=sqlite:///test.db
EOF2

cat > test_env.py << 'PYEOF2'
from dotenv import load_dotenv
import os

load_dotenv()

print(f"App Name: {os.getenv('APP_NAME')}")
print(f"Version: {os.getenv('APP_VERSION')}")
print(f"Secret: {os.getenv('SECRET')[:5]}***")  # Sirf first 5 chars
print(f"Database: {os.getenv('DATABASE')}")
PYEOF2

python test_env.py
deactivate && cd ~
```

---

## 📖 Summary

```
export VAR=value      = Env var set karo (current session)
echo $VAR             = Var value dekho
env                   = All vars dekho
unset VAR             = Var remove karo

.env file             = Local development config
.gitignore mein .env  = ALWAYS!
.env.example          = Template (no secrets)

python-dotenv         = .env file load karo
os.environ.get('VAR') = Python mein access
os.getenv('VAR', 'default') = With default value
```

---

**Next:** `06-Python-On-Linux/05-Git.md` → Git master karo! 🌿
