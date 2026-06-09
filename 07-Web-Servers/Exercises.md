# 🏋️ 07-Web-Servers: Exercises

---

## Exercise 1: Nginx Static Site

```bash
# Nginx install aur static site deploy:
sudo apt-get install nginx -y
sudo service nginx start 2>/dev/null || sudo systemctl start nginx

# Custom page:
cat | sudo tee /var/www/html/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>My Linux Site</title>
    <style>
        body { font-family: Arial, sans-serif; background: #1a1a2e; color: #e0e0e0; }
        .container { max-width: 600px; margin: 100px auto; text-align: center; }
        h1 { color: #16213e; background: #0f3460; padding: 20px; border-radius: 10px; color: white; }
        p { color: #a0a0a0; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐧 My Linux Web Server!</h1>
        <p>Served by Nginx</p>
        <p>Deployed via Terminal</p>
    </div>
</body>
</html>
EOF

curl -s http://localhost | grep "Linux Web Server"
echo "Static site working!"

# Logs check:
sudo tail -5 /var/log/nginx/access.log
```

---

## Exercise 2: Flask + Gunicorn Full Stack

```bash
mkdir ~/webserver_practice && cd ~/webserver_practice
python3 -m venv venv && source venv/bin/activate
pip install flask gunicorn

# Flask app:
cat > app.py << 'EOF'
from flask import Flask, jsonify
import datetime, os, socket

app = Flask(__name__)

@app.route('/')
def home():
    return f"""
    <h1>🚀 Flask Production App</h1>
    <ul>
        <li>Time: {datetime.datetime.now()}</li>
        <li>Host: {socket.gethostname()}</li>
        <li>PID: {os.getpid()}</li>
    </ul>
    """

@app.route('/api/v1/status')
def status():
    return jsonify({
        'status': 'running',
        'timestamp': str(datetime.datetime.now()),
        'hostname': socket.gethostname(),
        'pid': os.getpid()
    })

@app.route('/api/v1/echo', methods=['POST'])
def echo():
    from flask import request
    data = request.json or {}
    return jsonify({'received': data, 'echo': 'OK'})
EOF

# Gunicorn run:
gunicorn --workers 2 --bind 0.0.0.0:5002 app:app --daemon --pid /tmp/gunicorn.pid

sleep 2

# Test:
echo "Testing Flask + Gunicorn:"
curl -s http://localhost:5002/api/v1/status | python3 -m json.tool

curl -s -X POST http://localhost:5002/api/v1/echo \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello from Linux!"}' | python3 -m json.tool

# Stop:
if [ -f /tmp/gunicorn.pid ]; then
    kill $(cat /tmp/gunicorn.pid)
    rm /tmp/gunicorn.pid
fi

deactivate && cd ~
```

---

## 🔥 Mini Project: Multi-Endpoint API

```bash
mkdir ~/mini_api && cd ~/mini_api
python3 -m venv venv && source venv/bin/activate
pip install flask gunicorn requests

cat > api.py << 'EOF'
from flask import Flask, jsonify, request
from datetime import datetime
import random, os

app = Flask(__name__)

# Simple "database":
todos = [
    {"id": 1, "task": "Learn Linux", "done": True},
    {"id": 2, "task": "Deploy Flask", "done": False},
    {"id": 3, "task": "Learn Docker", "done": False},
]

@app.route('/api/todos', methods=['GET'])
def get_todos():
    filter_done = request.args.get('done')
    if filter_done is not None:
        filtered = [t for t in todos if str(t['done']).lower() == filter_done.lower()]
        return jsonify(filtered)
    return jsonify(todos)

@app.route('/api/todos/<int:todo_id>', methods=['GET'])
def get_todo(todo_id):
    todo = next((t for t in todos if t['id'] == todo_id), None)
    if not todo:
        return jsonify({'error': 'Not found'}), 404
    return jsonify(todo)

@app.route('/api/todos', methods=['POST'])
def add_todo():
    data = request.json
    if not data or 'task' not in data:
        return jsonify({'error': 'task field required'}), 400
    new_todo = {
        'id': max(t['id'] for t in todos) + 1,
        'task': data['task'],
        'done': False
    }
    todos.append(new_todo)
    return jsonify(new_todo), 201

@app.route('/api/todos/<int:todo_id>', methods=['PUT'])
def update_todo(todo_id):
    todo = next((t for t in todos if t['id'] == todo_id), None)
    if not todo:
        return jsonify({'error': 'Not found'}), 404
    data = request.json or {}
    todo.update(data)
    return jsonify(todo)

@app.route('/health')
def health():
    return jsonify({'status': 'ok', 'uptime': str(datetime.now())})
EOF

# Run aur test:
python3 api.py &
sleep 2

echo "=== API Tests ==="
echo "GET all todos:"
curl -s http://localhost:5000/api/todos | python3 -m json.tool

echo "GET one todo:"
curl -s http://localhost:5000/api/todos/1 | python3 -m json.tool

echo "POST new todo:"
curl -s -X POST http://localhost:5000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"task": "Learn Nginx"}' | python3 -m json.tool

echo "Filter done todos:"
curl -s "http://localhost:5000/api/todos?done=true" | python3 -m json.tool

kill %1
deactivate && cd ~
```

---

## ✅ Self-Check

- [ ] Nginx install aur configure kar sakta/sakti hoon
- [ ] Static website Nginx se serve kar sakta/sakti hoon
- [ ] Flask app Gunicorn se run kar sakta/sakti hoon
- [ ] Nginx reverse proxy setup kar sakta/sakti hoon
- [ ] FastAPI app run kar sakta/sakti hoon
- [ ] Systemd service create kar sakta/sakti hoon
- [ ] Nginx config test (nginx -t) kar sakta/sakti hoon

**Next Module:** `08-Docker` → Container revolution! 🐳
