# 📦 Lesson 3: pip — Python Package Manager

---

## 📚 Learning Objectives

- pip se packages install/uninstall karna
- requirements.txt manage karna
- Package versions handle karna
- pip advanced features

---

## 📦 pip kya hai?

**pip = Python's package manager = npm jaisa (JavaScript ke liye)**

PyPI (Python Package Index) pe 400,000+ packages hain!

---

## ⚡ Essential pip Commands

```bash
# Install:
pip install flask
pip install flask==2.3.0           # Specific version
pip install "flask>=2.0,<3.0"      # Version range
pip install flask requests gunicorn  # Multiple at once
pip install -r requirements.txt     # File se

# Uninstall:
pip uninstall flask
pip uninstall -y flask requests      # Confirmation skip

# List installed:
pip list
pip list --outdated                  # Purani packages

# Info:
pip show flask
pip show flask | grep -i "name\|version\|location"

# Search (PyPI pe):
pip search flask  # (deprecated, use pypi.org instead)

# Upgrade:
pip install --upgrade flask
pip install -U flask                 # Shortform
pip install --upgrade pip            # pip itself

# Freeze (requirements format):
pip freeze
pip freeze > requirements.txt

# Download without install:
pip download flask -d ./packages/
```

---

## 🔧 pip.conf — Global Config

```bash
# Config file:
cat ~/.pip/pip.conf  # Ya
cat ~/.config/pip/pip.conf

# Create:
mkdir -p ~/.config/pip
cat > ~/.config/pip/pip.conf << 'EOF'
[global]
timeout = 60
index-url = https://pypi.org/simple/
trusted-host = pypi.org

# Indian mirror (faster):
# index-url = https://pypi.tuna.tsinghua.edu.cn/simple/
EOF
```

---

## 🏋️ Practice

```bash
# Venv mein:
cd ~/venv_practice 2>/dev/null || mkdir ~/venv_practice && cd ~/venv_practice
python3 -m venv venv && source venv/bin/activate

pip install flask requests python-dotenv

# Info check:
pip show flask
pip list | grep -i flask
pip freeze > requirements.txt
cat requirements.txt

deactivate
cd ~
```

---

## 📖 Summary

```
pip install pkg         = Install
pip install pkg==1.0    = Specific version
pip install -r req.txt  = From file
pip uninstall pkg       = Remove
pip list                = Installed list
pip show pkg            = Package info
pip freeze > req.txt    = Save requirements
pip install -U pip      = Upgrade pip itself
```

**Next:** `06-Python-On-Linux/04-Environment-Variables.md` → Secrets manage karo! 🔧
