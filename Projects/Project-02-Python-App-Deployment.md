# 🚀 Project 2: Python App Complete Deployment

**Difficulty:** Intermediate | **Time:** 6-8 hours | **Module:** 06-07

---

## 🎯 Project Goal

Ek complete Flask CRUD application banao aur Linux pe Nginx + Gunicorn + systemd ke saath production deploy karo.

---

## 📋 What You Will Build

```
Flask Todo API
    ├── GET    /api/todos        → All todos
    ├── POST   /api/todos        → Create todo
    ├── PUT    /api/todos/<id>   → Update todo
    ├── DELETE /api/todos/<id>   → Delete todo
    └── GET    /health           → Health check
```

---

## 📁 Project Structure

```bash
mkdir -p ~/flask_todo_api/{logs}
cd ~/flask_todo_api
```

---

## 💻 Application Code

```bash
# 1. Flask app:
cat > app.py << 'EOF'
from flask import Flask, jsonify, request
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime
import os
from dotenv import load_dotenv
import logging

load_dotenv()

logging.basicConfig(
    filename='logs/app.log',
    level=logging.INFO,
    format='%(asctime)s %(levelname)s %(message)s'
)

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv(
    'DATABASE_URL', 'sqlite:///todos.db'
)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY', 'dev-secret')

db = SQLAlchemy(app)

class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    description = db.Column(db.Text)
    done = db.Column(db.Boolean, default=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    def to_dict(self):
        return {
            'id': self.id,
            'title': self.title,
            'description': self.description,
            'done': self.done,
            'created_at': str(self.created_at)
        }

with app.app_context():
    db.create_all()

@app.route('/health')
def health():
    return jsonify({'status': 'healthy', 'timestamp': str(datetime.now())})

@app.route('/api/todos', methods=['GET'])
def get_todos():
    todos = Todo.query.all()
    return jsonify([t.to_dict() for t in todos])

@app.route('/api/todos/<int:id>', methods=['GET'])
def get_todo(id):
    todo = Todo.query.get_or_404(id)
    return jsonify(todo.to_dict())

@app.route('/api/todos', methods=['POST'])
def create_todo():
    data = request.json
    if not data or 'title' not in data:
        return jsonify({'error': 'title required'}), 400
    todo = Todo(
        title=data['title'],
        description=data.get('description', '')
    )
    db.session.add(todo)
    db.session.commit()
    app.logger.info(f'Created todo: {todo.title}')
    return jsonify(todo.to_dict()), 201

@app.route('/api/todos/<int:id>', methods=['PUT'])
def update_todo(id):
    todo = Todo.query.get_or_404(id)
    data = request.json or {}
    if 'title' in data:
        todo.title = data['title']
    if 'done' in data:
        todo.done = data['done']
    if 'description' in data:
        todo.description = data['description']
    db.session.commit()
    return jsonify(todo.to_dict())

@app.route('/api/todos/<int:id>', methods=['DELETE'])
def delete_todo(id):
    todo = Todo.query.get_or_404(id)
    db.session.delete(todo)
    db.session.commit()
    return jsonify({'message': f'Todo {id} deleted'}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

# 2. Requirements:
cat > requirements.txt << 'EOF'
flask==2.3.0
flask-sqlalchemy==3.0.5
gunicorn==20.1.0
python-dotenv==0.21.0
EOF

# 3. .env:
cat > .env << 'EOF'
DATABASE_URL=sqlite:///todos.db
SECRET_KEY=project-secret-key-123
FLASK_ENV=production
EOF

# 4. Dockerfile:
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN mkdir -p logs
EXPOSE 5000
CMD ["gunicorn", "--workers", "3", "--bind", "0.0.0.0:5000", "app:app"]
EOF
```

---

## 🚀 Deployment

```bash
cd ~/flask_todo_api

# Venv setup:
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Test locally:
python3 app.py &
sleep 2
curl http://localhost:5000/health

# Create test todos:
curl -X POST http://localhost:5000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":"Learn Linux","description":"Complete 90-day plan"}'

curl -X POST http://localhost:5000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title":"Deploy Flask","description":"Nginx + Gunicorn"}'

curl http://localhost:5000/api/todos | python3 -m json.tool

# Mark done:
curl -X PUT http://localhost:5000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"done":true}'

kill %1

# Production with Gunicorn:
gunicorn --workers 3 --bind 0.0.0.0:5001 app:app --daemon
sleep 2
curl http://localhost:5001/health
pkill gunicorn

deactivate
```

---

## ✅ Success Criteria

- [ ] All 5 API endpoints kaam kar rahe hain
- [ ] Data SQLite mein persist ho raha hai
- [ ] Gunicorn se production serve ho raha hai
- [ ] Health check endpoint kaam karta hai
- [ ] Logs file mein aa rahi hain

**Next: Ise Docker mein dalenge! (Project 3) 🐳**
