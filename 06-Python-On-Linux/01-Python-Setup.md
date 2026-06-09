# 🐍 Lesson 1: Python Setup on Linux

---

## 📚 Learning Objectives

- Linux pe Python installation verify karna
- Python versions manage karna
- pip setup karna
- pyenv use karna (multiple Python versions)

---

## 🔍 Python Check Karna

```bash
# Python versions:
python3 --version
python3 -V
python --version 2>/dev/null || echo "python2 not installed"

# Python kahan hai:
which python3
which python

# All Python installations:
ls /usr/bin/python*

# pip check:
pip3 --version
pip --version 2>/dev/null
```

---

## 📦 Python Install Karna (agar nahi hai)

```bash
# Ubuntu/Debian mein:
sudo apt-get update
sudo apt-get install python3 python3-pip python3-venv

# Specific version:
sudo apt-get install python3.11
sudo apt-get install python3.11-venv python3.11-pip

# Development headers (C extensions ke liye):
sudo apt-get install python3-dev build-essential

# Verify:
python3 --version
pip3 --version
```

---

## 🐍 pyenv — Multiple Python Versions

```bash
# pyenv install karo:
curl https://pyenv.run | bash

# ~/.bashrc mein add karo:
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc

# Reload:
source ~/.bashrc

# Available versions:
pyenv install --list | grep " 3\."

# Specific version install:
pyenv install 3.11.0
pyenv install 3.10.9

# Global version set:
pyenv global 3.11.0
python3 --version  # 3.11.0

# Project-specific version:
cd myproject
pyenv local 3.10.9  # .python-version file create hoga
python3 --version   # 3.10.9

# Installed versions:
pyenv versions
```

---

## 🔗 Python Aliases Setup

```bash
# ~/.bashrc mein add karo:
alias python=python3
alias pip=pip3

# Reload:
source ~/.bashrc

# Test:
python --version  # Ab python3 chalega
```

---

## 📊 Python Interactive Shell

```bash
# Python REPL:
python3

# Quit:
exit() ya Ctrl+D

# IPython (better REPL):
pip3 install ipython
ipython

# Jupyter notebook:
pip3 install jupyter
jupyter notebook
```

---

## 🏋️ Practice

```bash
# 1. System Python info
python3 -V
pip3 -V
python3 -c "import sys; print(sys.version)"
python3 -c "import sys; print(sys.path)"

# 2. Python kahan hai
which python3
python3 -c "import sys; print(sys.executable)"

# 3. Installed packages list
pip3 list
pip3 list | wc -l    # Count

# 4. Quick Python test
python3 << 'PYEOF2'
import platform
import sys

print(f"Python: {sys.version}")
print(f"Platform: {platform.system()} {platform.release()}")
print(f"Architecture: {platform.machine()}")
print("Linux + Python = Best combo!")
PYEOF2
```

---

## 📖 Summary

```
python3 --version    = Python version check
which python3        = Python location
pip3 install pkg     = Package install
pip3 list            = Installed packages
pyenv install X.Y.Z  = Specific Python version
pyenv global X.Y.Z   = Default Python set
pyenv local X.Y.Z    = Project-specific Python
```

---

**Next:** `06-Python-On-Linux/02-Virtual-Environments.md` → venv master karo! 🌿
