# 🌐 Lesson 4: curl & wget — Terminal se Internet!

---

## 📚 Learning Objectives

- curl se HTTP requests karna
- wget se files download karna
- APIs test karna terminal se
- Real-world uses

---

## 🔧 curl — Swiss Army Knife of HTTP

**curl = Client URL — practically har kaam ka tool!**

```bash
# Basic GET request:
curl https://api.github.com

# Response body only:
curl -s https://api.github.com/users/torvalds

# Headers bhi dekho:
curl -I https://google.com    # Headers only
curl -v https://google.com    # Verbose (request + response)

# Silent (no progress):
curl -s https://api.github.com/users/torvalds

# Save to file:
curl -O https://example.com/file.txt       # Same name
curl -o myfile.txt https://example.com/file.txt  # Custom name

# Follow redirects:
curl -L https://google.com   # 301/302 follow karo

# Timeout:
curl --max-time 10 https://api.example.com  # 10 sec timeout
```

---

## 📡 API Requests with curl

```bash
# GET request:
curl https://jsonplaceholder.typicode.com/posts/1

# GET with headers:
curl -H "Authorization: Bearer TOKEN" https://api.example.com/data
curl -H "Content-Type: application/json" https://api.example.com

# POST request:
curl -X POST https://jsonplaceholder.typicode.com/posts \
  -H "Content-Type: application/json" \
  -d '{"title": "Test", "body": "Content", "userId": 1}'

# POST with form data:
curl -X POST https://example.com/login \
  -d "username=admin&password=secret"

# PUT request:
curl -X PUT https://jsonplaceholder.typicode.com/posts/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Updated"}'

# DELETE request:
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1

# Pretty print JSON:
curl -s https://jsonplaceholder.typicode.com/posts/1 | python3 -m json.tool
```

---

## 🔑 Useful curl Options

```bash
# Authentication:
curl -u username:password https://api.example.com
curl -H "Authorization: Bearer TOKEN" https://api.example.com

# SSL ignore (TESTING ONLY!):
curl -k https://self-signed.example.com

# Upload file:
curl -F "file=@/path/to/file.txt" https://upload.example.com

# Set user agent:
curl -A "MyApp/1.0" https://example.com

# Cookies:
curl -c cookies.txt https://example.com/login  # Save cookies
curl -b cookies.txt https://example.com/profile  # Use cookies

# Rate limiting check:
curl -I https://api.github.com  # Check X-RateLimit headers
```

---

## 📥 wget — File Downloader

```bash
# Basic download:
wget https://example.com/file.zip

# Quiet mode:
wget -q https://example.com/file.zip

# Custom filename:
wget -O myfile.zip https://example.com/file.zip

# Continue interrupted download:
wget -c https://example.com/large_file.zip

# Background download:
wget -b https://example.com/large_file.zip

# Recursive download (entire website):
wget -r -l 2 https://example.com    # 2 levels deep

# Only specific file types:
wget -r -A "*.pdf" https://example.com

# Mirror website:
wget --mirror --convert-links https://example.com

# Rate limit:
wget --limit-rate=1m https://example.com/large_file.zip  # 1MB/s max
```

---

## 🐍 Python Developer ke liye

```bash
# Flask app test karo:
# Start karo pehle:
python3 -c "
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello!'

@app.route('/api/data')
def data():
    return {'message': 'API works!', 'status': 200}

app.run(port=5001)
" &

sleep 2  # Wait for startup

# Test endpoints:
curl http://localhost:5001/
curl http://localhost:5001/api/data
curl -s http://localhost:5001/api/data | python3 -m json.tool

# Kill the test server
pkill -f "python3 -c"
```

---

## 🏋️ Practice

```bash
# 1. Public API test karo
curl -s "https://jsonplaceholder.typicode.com/posts/1" | python3 -m json.tool

# 2. Headers dekho
curl -I https://python.org 2>/dev/null | head -15

# 3. Your public IP
curl -s ifconfig.me && echo ""

# 4. Download + check
curl -s "https://jsonplaceholder.typicode.com/users" | python3 -c "
import json, sys
users = json.load(sys.stdin)
for u in users[:3]:
    print(f'Name: {u[\"name\"]}, Email: {u[\"email\"]}')
"
```

---

## 📖 Summary

```
curl URL              = GET request
curl -X POST URL -d   = POST request
curl -H "Header: val" = Custom header
curl -s               = Silent mode
curl -o file URL      = Download to file
curl -I URL           = Headers only
wget URL              = Download file
wget -c URL           = Resume download
wget -O name URL      = Custom filename
```

---

**Next:** `05-Networking/05-SSH.md` → Remote server connect karo! 🔒
