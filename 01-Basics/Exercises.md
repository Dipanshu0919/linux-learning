# 🏋️ 01-Basics: Exercises & Challenges

---

## 📋 Exercise 1: Terminal Navigation Master

### Goal: cd, pwd, ls ke sath confident ho jaao

```bash
# Step 1: Pata karo tum kahan ho
pwd

# Step 2: Sab files/folders dekho
ls -la

# Step 3: 01-Basics folder mein jao
cd 01-Basics

# Step 4: Confirm karo
pwd

# Step 5: Files dekho
ls

# Step 6: Home folder pe jao
cd ~
pwd

# Step 7: Previous location pe wapas jao
cd -
pwd
```

**Expected:** Har pwd command location confirm kare. Koi error nahi aani chahiye.

---

## 📋 Exercise 2: Project Structure Banana

### Goal: mkdir aur touch ka use

```bash
# Ye structure banao:
# flask_blog/
#     app.py
#     config.py
#     requirements.txt
#     README.md
#     templates/
#         index.html
#         about.html
#     static/
#         style.css
#     tests/
#         test_app.py

# Solution:
mkdir -p flask_blog/{templates,static,tests}
touch flask_blog/app.py
touch flask_blog/config.py
touch flask_blog/requirements.txt
touch flask_blog/README.md
touch flask_blog/templates/{index.html,about.html}
touch flask_blog/static/style.css
touch flask_blog/tests/test_app.py

# Verify karo:
find flask_blog -type f
find flask_blog -type d
```

**Expected output:**
```
flask_blog/app.py
flask_blog/config.py
flask_blog/requirements.txt
flask_blog/README.md
flask_blog/templates/index.html
flask_blog/templates/about.html
flask_blog/static/style.css
flask_blog/tests/test_app.py
```

---

## 📋 Exercise 3: File Content Operations

### Goal: cat, echo, grep ka use

```bash
# Step 1: requirements.txt mein packages add karo
echo "flask==2.3.0" > flask_blog/requirements.txt
echo "gunicorn==20.1.0" >> flask_blog/requirements.txt
echo "python-dotenv==0.21.0" >> flask_blog/requirements.txt
echo "SQLAlchemy==2.0.0" >> flask_blog/requirements.txt

# Step 2: Verify karo
cat flask_blog/requirements.txt

# Step 3: Specific package search karo
grep "flask" flask_blog/requirements.txt

# Step 4: Line count
wc -l flask_blog/requirements.txt

# Step 5: app.py mein basic Flask code likhna
cat > flask_blog/app.py << 'APPEOF'
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Linux!"

@app.route('/about')
def about():
    return "About page"

if __name__ == '__main__':
    app.run(debug=True)
APPEOF

# Verify:
cat flask_blog/app.py
```

---

## 📋 Exercise 4: File Operations Challenge

### Goal: cp, mv, rm commands

```bash
# Situation: Backup create karo

# Step 1: backup folder banao
mkdir flask_blog_backup

# Step 2: Important files backup karo
cp flask_blog/app.py flask_blog_backup/
cp flask_blog/requirements.txt flask_blog_backup/
cp -r flask_blog/templates/ flask_blog_backup/

# Step 3: Verify backup
ls flask_blog_backup/

# Step 4: Ek file rename karo
mv flask_blog/config.py flask_blog/configuration.py

# Verify rename
ls flask_blog/

# Step 5: Puraani backup file delete karo
rm flask_blog_backup/app.py

# Verify
ls flask_blog_backup/
```

---

## 📋 Exercise 5: System Investigation

### Goal: System commands use karna

```bash
# Q1: Tumhara username kya hai?
whoami

# Q2: Tumhara home folder kahan hai?
echo $HOME

# Q3: Kitna disk space available hai?
df -h

# Q4: Kitni RAM hai?
free -h

# Q5: CPU ka naam kya hai?
cat /proc/cpuinfo | grep "model name" | head -1

# Q6: System kitni der se chala hai?
uptime

# Q7: Abhi ka timestamp
date "+%Y-%m-%d %H:%M:%S"
```

---

## 🔥 Mini Project: "Linux System Report"

Ek script likhni hai jo system report generate kare!

```bash
# report.txt file banao
echo "=== SYSTEM REPORT ===" > report.txt
echo "Date: $(date)" >> report.txt
echo "User: $(whoami)" >> report.txt
echo "Hostname: $(hostname)" >> report.txt
echo "Uptime: $(uptime)" >> report.txt
echo "" >> report.txt
echo "=== DISK USAGE ===" >> report.txt
df -h >> report.txt
echo "" >> report.txt
echo "=== MEMORY ===" >> report.txt
free -h >> report.txt
echo "" >> report.txt
echo "=== LINUX VERSION ===" >> report.txt
cat /etc/os-release >> report.txt

# Report dekho
cat report.txt

# Line count
wc -l report.txt
```

---

## 🎯 Challenge Level Questions

**Challenge 1:** Flask project mein saari `.py` files dhundho
```bash
find flask_blog -name "*.py"
```

**Challenge 2:** Requirements.txt mein kitni packages hain?
```bash
wc -l flask_blog/requirements.txt
```

**Challenge 3:** Saari files jo 1 hour mein modify hui hoon
```bash
find flask_blog -mmin -60
```

**Challenge 4:** Saari hidden files aur folders dekho
```bash
ls -la ~
```

**Challenge 5:** Biggest file kaun si hai?
```bash
du -sh flask_blog/* | sort -h
```

---

## ✅ Self-Check Checklist

Ye sab kar sako toh Phase 1 complete maano:

- [ ] Terminal confidently open aur close kar sakta/sakti hoon
- [ ] pwd se current location check kar sakta/sakti hoon
- [ ] ls, ls -la se files dekh sakta/sakti hoon
- [ ] cd se folders navigate kar sakta/sakti hoon
- [ ] mkdir -p se nested folders bana sakta/sakti hoon
- [ ] touch se empty files bana sakta/sakti hoon
- [ ] cat, head, tail se files read kar sakta/sakti hoon
- [ ] cp aur mv se files copy/move kar sakta/sakti hoon
- [ ] grep se text search kar sakta/sakti hoon
- [ ] find se files dhundh sakta/sakti hoon
- [ ] df -h aur free -h se system info dekh sakta/sakti hoon
- [ ] echo se files mein text likh sakta/sakti hoon

**Agar sab tick hai — Next chapter pe jao! 🎉**
