# 🌿 Lesson 2: Virtual Environments — Project Isolation

---

## 📚 Learning Objectives

- Virtual environment kya hai aur kyun chahiye
- venv se environment create karna
- Packages install aur manage karna
- virtualenv vs venv
- Best practices

---

## 🤔 Virtual Environment kyun chahiye?

```
Bina venv:
    System Python
        ├── flask==2.0.0   (Project A chahta hai)
        ├── flask==3.0.0   (Project B chahta hai)
        └── CONFLICT! Dono ek saath nahi chal sakte!

Venv ke saath:
    System Python (clean!)
    Project A venv/
        └── flask==2.0.0  ✓
    Project B venv/
        └── flask==3.0.0  ✓
    Koi conflict nahi!
```

**Python developer ke liye rule #1:** Har project ke liye alag virtual environment!

---

## 🛠️ venv — Built-in Solution

```bash
# venv create karo:
python3 -m venv venv

# Ya custom naam:
python3 -m venv myenv
python3 -m venv .venv    # Hidden folder (convention)

# Specific Python version:
python3.11 -m venv venv

# Activate karo:
source venv/bin/activate        # Linux/Mac
# Ya:
. venv/bin/activate

# Prompt change ho jaayega:
(venv) user@hostname:~/project$
#^^^^^
# Ye confirm karta hai venv active hai!

# Deactivate:
deactivate

# venv folder structure:
ls venv/
# bin/  include/  lib/  lib64  pyvenv.cfg
```

---

## 📦 Venv ke andar packages:

```bash
# Activate ke baad:
source venv/bin/activate

# pip automatically venv mein install karta hai:
pip install flask
pip install requests sqlalchemy

# List:
pip list
pip freeze   # requirements.txt format mein

# requirements.txt save karo:
pip freeze > requirements.txt

# requirements.txt se install karo:
pip install -r requirements.txt

# Specific version:
pip install flask==2.3.0
pip install "flask>=2.0,<3.0"

# Upgrade:
pip install --upgrade flask
pip install -U pip   # pip itself upgrade
```

---

## 📋 requirements.txt Best Practices

```bash
# requirements.txt example:
cat requirements.txt

flask==2.3.0
gunicorn==20.1.0
SQLAlchemy==2.0.0
python-dotenv==0.21.0
requests==2.28.0
```

### Development vs Production:

```bash
# requirements.txt (production - exact versions):
flask==2.3.0
gunicorn==20.1.0

# requirements-dev.txt (development extras):
-r requirements.txt  # Include production packages
pytest==7.3.0
black==23.1.0
flake8==6.0.0
ipython==8.10.0
```

---

## 🚀 Typical Project Workflow

```bash
# Naya project start karo:
mkdir my_flask_project
cd my_flask_project

# venv banao:
python3 -m venv venv

# Activate karo:
source venv/bin/activate

# Packages install karo:
pip install flask gunicorn python-dotenv

# Requirements save karo:
pip freeze > requirements.txt

# Code likhna shuru karo...
nano app.py

# GitHub pe push karne se pehle:
# .gitignore mein venv/ add karo!
echo "venv/" >> .gitignore
echo "__pycache__/" >> .gitignore
echo "*.pyc" >> .gitignore
echo ".env" >> .gitignore

# Deactivate karo:
deactivate
```

---

## 📁 .gitignore — venv ko git mein mat daalo!

```bash
cat > .gitignore << 'EOF'
# Virtual Environment
venv/
.venv/
env/
ENV/

# Python
__pycache__/
*.py[cod]
*$py.class
*.egg-info/
dist/
build/

# Environment variables
.env
.env.*
!.env.example  # Example file rakho

# Database
*.db
*.sqlite3

# Logs
*.log
logs/

# OS
.DS_Store
Thumbs.db
EOF
```

---

## 🔧 virtualenv (Older, but popular)

```bash
# Install:
pip3 install virtualenv

# Create:
virtualenv venv
virtualenv -p python3.11 venv    # Specific Python

# Activate (same as venv):
source venv/bin/activate
```

---

## 🏋️ Practice

```bash
# Complete venv workflow:

# 1. Project folder banao:
mkdir ~/venv_practice && cd ~/venv_practice

# 2. venv create karo:
python3 -m venv venv

# 3. Activate karo:
source venv/bin/activate

# 4. Prompt dekho (venv) hona chahiye
echo "Active env: $VIRTUAL_ENV"

# 5. Packages install karo:
pip install flask requests

# 6. Installed packages dekho:
pip list

# 7. Requirements save:
pip freeze > requirements.txt
cat requirements.txt

# 8. Python location (venv ke andar hona chahiye):
which python
which pip

# 9. Quick test:
python -c "import flask; print('Flask', flask.__version__, 'installed!')"

# 10. Deactivate:
deactivate
echo "Back to system: $(which python3)"

cd ..
```

---

## 📖 Summary

```
python3 -m venv venv      = Virtual environment create
source venv/bin/activate  = Activate karo
deactivate                = Deactivate karo
pip install pkg           = Package install (venv mein)
pip freeze > req.txt      = Save requirements
pip install -r req.txt    = Install from requirements
(venv)                    = Active hone ka sign
venv/ in .gitignore       = ALWAYS!
```

---

**Next:** `06-Python-On-Linux/03-Pip.md` → pip master karo! 📦
