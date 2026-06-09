# ⚡ Lesson 7: FastAPI Deployment — Modern Python API

---

## 📚 Learning Objectives

- FastAPI production deployment
- Uvicorn aur Gunicorn combination
- Nginx reverse proxy setup
- Auto-generated docs (Swagger)

---

## ⚡ FastAPI kya hai?

```
FastAPI = Modern, fast Python web framework
- Automatic API docs (Swagger UI!)
- Type hints se validation
- Async support
- 2-3x faster than Flask for I/O tasks
```

---

## 📦 FastAPI App banao

```bash
mkdir -p /var/www/myfastapiapp
cd /var/www/myfastapiapp

# Venv:
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn[standard] gunicorn python-dotenv pydantic

# requirements.txt:
pip freeze > requirements.txt

# main.py:
cat > main.py << 'EOF'
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional, List
import os
from datetime import datetime

app = FastAPI(
    title="My Linux API",
    description="FastAPI deployed on Linux with Nginx",
    version="1.0.0"
)

# Pydantic models (auto-validation!):
class Item(BaseModel):
    id: Optional[int] = None
    name: str
    description: Optional[str] = None
    price: float

class ItemResponse(BaseModel):
    message: str
    item: Item

# In-memory storage (replace with DB in production):
items_db: List[Item] = [
    Item(id=1, name="Laptop", description="Gaming laptop", price=75000.0),
    Item(id=2, name="Mouse", description="Wireless mouse", price=1500.0),
]

@app.get("/")
async def root():
    return {
        "message": "FastAPI on Linux!",
        "docs": "/docs",
        "time": str(datetime.now())
    }

@app.get("/health")
async def health():
    return {"status": "healthy", "environment": os.getenv("ENV", "production")}

@app.get("/items", response_model=List[Item])
async def get_items():
    return items_db

@app.get("/items/{item_id}", response_model=Item)
async def get_item(item_id: int):
    item = next((i for i in items_db if i.id == item_id), None)
    if not item:
        raise HTTPException(status_code=404, detail=f"Item {item_id} not found")
    return item

@app.post("/items", response_model=ItemResponse)
async def create_item(item: Item):
    item.id = max([i.id for i in items_db], default=0) + 1
    items_db.append(item)
    return ItemResponse(message="Item created!", item=item)

@app.delete("/items/{item_id}")
async def delete_item(item_id: int):
    global items_db
    item = next((i for i in items_db if i.id == item_id), None)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    items_db = [i for i in items_db if i.id != item_id]
    return {"message": f"Item {item_id} deleted"}
EOF
```

---

## 🔄 Running FastAPI

```bash
# Development (auto-reload):
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Test karo:
curl http://localhost:8000/
curl http://localhost:8000/items
curl http://localhost:8000/docs   # Swagger UI (browser mein!)

# Production (Uvicorn workers):
uvicorn main:app --workers 4 --host 127.0.0.1 --port 8000

# Production (Gunicorn + Uvicorn workers - Recommended!):
gunicorn main:app \
    --workers 4 \
    --worker-class uvicorn.workers.UvicornWorker \
    --bind 127.0.0.1:8000 \
    --access-logfile logs/access.log \
    --error-logfile logs/error.log
```

---

## ⚙️ Systemd Service

```bash
mkdir logs

sudo tee /etc/systemd/system/myfastapiapp.service << 'EOF'
[Unit]
Description=FastAPI Application
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myfastapiapp
Environment="PATH=/var/www/myfastapiapp/venv/bin"
ExecStart=/var/www/myfastapiapp/venv/bin/gunicorn \
    main:app \
    --workers 4 \
    --worker-class uvicorn.workers.UvicornWorker \
    --bind 127.0.0.1:8000 \
    --access-logfile /var/www/myfastapiapp/logs/access.log \
    --error-logfile /var/www/myfastapiapp/logs/error.log
ExecReload=/bin/kill -s HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable myfastapiapp
sudo systemctl start myfastapiapp
sudo systemctl status myfastapiapp
```

---

## 🌐 Nginx Config

```bash
sudo tee /etc/nginx/sites-available/myfastapiapp << 'EOF'
server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support (FastAPI ke liye):
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # Swagger docs allow karo (production mein disable bhi kar sakte ho):
    location /docs {
        proxy_pass http://127.0.0.1:8000/docs;
    }

    location /openapi.json {
        proxy_pass http://127.0.0.1:8000/openapi.json;
    }

    access_log /var/log/nginx/fastapi_access.log;
    error_log /var/log/nginx/fastapi_error.log;
}
EOF

sudo ln -sf /etc/nginx/sites-available/myfastapiapp /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

---

## 🧪 API Testing

```bash
# Basic tests:
curl http://localhost:8000/
curl http://localhost:8000/health
curl http://localhost:8000/items

# Create item:
curl -X POST http://localhost:8000/items \
  -H "Content-Type: application/json" \
  -d '{"name":"Keyboard","description":"Mechanical keyboard","price":5000.0}'

# Get specific item:
curl http://localhost:8000/items/1

# Delete item:
curl -X DELETE http://localhost:8000/items/1

# Swagger UI (browser mein):
# http://localhost:8000/docs
```

---

## 🏋️ Practice

```bash
mkdir ~/fastapi_demo && cd ~/fastapi_demo
python3 -m venv venv && source venv/bin/activate
pip install fastapi "uvicorn[standard]" gunicorn

cat > main.py << 'EOF'
from fastapi import FastAPI
app = FastAPI(title="Linux Learning API")

@app.get("/")
async def root():
    return {"message": "FastAPI is running!", "status": "ok"}

@app.get("/ping")
async def ping():
    return {"pong": True}
EOF

# Background mein run:
uvicorn main:app --host 0.0.0.0 --port 8001 &
sleep 2

# Test:
curl http://localhost:8001/
curl http://localhost:8001/ping

# Kill:
pkill -f "uvicorn main:app"
deactivate && cd ~
```

---

## 📖 Summary

```
FastAPI = Modern async Python API framework
uvicorn main:app --reload   = Development
gunicorn + UvicornWorker    = Production (Best!)
/docs                       = Swagger UI (auto!)
/openapi.json               = API schema
Pydantic models             = Auto-validation
Type hints                  = FastAPI magic
```

**Next:** `07-Web-Servers/Exercises.md` → Web server exercises! 🌐
