# 🏋️ 06-Python-On-Linux: Exercises

---

## Exercise 1: Complete Flask Project Setup

```bash
# Complete project from scratch:
mkdir ~/linux_flask_project && cd ~/linux_flask_project

# 1. Git init:
git init

# 2. venv:
python3 -m venv venv
source venv/bin/activate

# 3. Packages:
pip install flask python-dotenv gunicorn requests

# 4. .gitignore:
cat > .gitignore << 'EOF'
venv/
__pycache__/
*.pyc
.env
*.log
EOF

# 5. .env file:
cat > .env << 'EOF'
FLASK_ENV=development
SECRET_KEY=dev-secret-key-123
PORT=5000
DEBUG=True
EOF

cat > .env.example << 'EOF'
FLASK_ENV=development
SECRET_KEY=change-this-to-random-string
PORT=5000
DEBUG=False
EOF

# 6. Main app:
cat > app.py << 'EOF'
from flask import Flask, jsonify
from dotenv import load_dotenv
import os

load_dotenv()

app = Flask(__name__)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')

@app.route('/')
def home():
    return jsonify({'message': 'Hello Linux!', 'env': os.getenv('FLASK_ENV')})

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(
        host='0.0.0.0',
        port=int(os.getenv('PORT', 5000)),
        debug=os.getenv('DEBUG', 'False') == 'True'
    )
EOF

# 7. Requirements:
pip freeze > requirements.txt

# 8. First commit:
git add .
git commit -m "Initial Flask project setup"

# 9. Run karo:
python3 app.py &
sleep 2
curl -s http://localhost:5000/ | python3 -m json.tool
curl -s http://localhost:5000/health | python3 -m json.tool
kill %1
deactivate && cd ~
```

---

## Exercise 2: Cron Job Setup

```bash
# Script banao:
cat > ~/daily_report.py << 'EOF'
#!/usr/bin/env python3
import datetime
import subprocess
import os

def get_disk_usage():
    result = subprocess.run(['df', '-h', '/'], capture_output=True, text=True)
    lines = result.stdout.strip().split('\n')
    return lines[1] if len(lines) > 1 else "N/A"

def get_memory():
    result = subprocess.run(['free', '-h'], capture_output=True, text=True)
    lines = result.stdout.strip().split('\n')
    return lines[1] if len(lines) > 1 else "N/A"

report = f"""
=== Daily System Report ===
Date: {datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
User: {os.getenv('USER', 'unknown')}

Disk: {get_disk_usage()}
Memory: {get_memory()}
===========================
"""

print(report)

# Log file mein bhi save karo:
with open(os.path.expanduser('~/daily_reports.log'), 'a') as f:
    f.write(report)
EOF

chmod +x ~/daily_report.py
python3 ~/daily_report.py

# Cron mein add karo (uncomment for real):
# (crontab -l 2>/dev/null; echo "0 8 * * * /usr/bin/python3 /home/codespace/daily_report.py") | crontab -
# crontab -l   # Verify
```

---

## ✅ Self-Check

- [ ] Python venv create, activate, deactivate kar sakta/sakti hoon
- [ ] pip install, freeze, requirements karo
- [ ] .env file use karke Python config manage kar sakta/sakti hoon
- [ ] Flask app run kar sakta/sakti hoon
- [ ] Gunicorn se production Flask run kar sakta/sakti hoon
- [ ] Git workflow use kar sakta/sakti hoon (init, add, commit)
- [ ] Cron job syntax samjhta/samjhti hoon

**Next Module:** `07-Web-Servers` → Nginx + Deployment! 🌐
