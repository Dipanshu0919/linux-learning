# 🏋️ 08-Docker: Complete Exercises

---

## Exercise 1: Dockerize a Python Script

```bash
mkdir ~/docker_exercises && cd ~/docker_exercises

# Python script banao:
cat > number_cruncher.py << 'EOF'
import sys
import random

def prime_check(n):
    if n < 2:
        return False
    for i in range(2, int(n**0.5)+1):
        if n % i == 0:
            return False
    return True

count = int(sys.argv[1]) if len(sys.argv) > 1 else 10
primes = [n for n in range(2, 10000) if prime_check(n)][:count]
print(f"First {count} prime numbers: {primes}")
EOF

# Dockerfile:
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app
COPY number_cruncher.py .
ENTRYPOINT ["python3", "number_cruncher.py"]
CMD ["10"]
EOF

if command -v docker &>/dev/null; then
    docker build -t number_cruncher .
    docker run --rm number_cruncher          # Default: 10 primes
    docker run --rm number_cruncher 20       # 20 primes
fi
```

---

## Exercise 2: Flask + Redis Compose

```bash
mkdir ~/redis_demo && cd ~/redis_demo

cat > app.py << 'EOF'
from flask import Flask, jsonify
import os
try:
    import redis
    r = redis.from_url(os.getenv('REDIS_URL', 'redis://localhost:6379'))
    REDIS_OK = True
except:
    REDIS_OK = False

app = Flask(__name__)

@app.route('/')
def home():
    if REDIS_OK:
        count = r.incr('visit_count')
        return jsonify({'visits': count, 'message': 'Redis working!'})
    return jsonify({'message': 'Redis not connected', 'visits': -1})

@app.route('/reset')
def reset():
    if REDIS_OK:
        r.set('visit_count', 0)
        return jsonify({'message': 'Counter reset!'})
    return jsonify({'error': 'Redis not available'})
EOF

cat > requirements.txt << 'EOF'
flask==2.3.0
redis==4.5.0
gunicorn==20.1.0
EOF

cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
CMD ["gunicorn", "--workers", "2", "--bind", "0.0.0.0:5000", "app:app"]
EOF

cat > docker-compose.yml << 'EOF'
version: '3.8'
services:
  web:
    build: .
    ports: ["5000:5000"]
    environment:
      REDIS_URL: redis://cache:6379
    depends_on: [cache]
    networks: [app_net]
  cache:
    image: redis:7-alpine
    networks: [app_net]
networks:
  app_net:
EOF

if command -v docker &>/dev/null; then
    docker-compose up -d --build
    sleep 5
    for i in 1 2 3; do curl -s http://localhost:5000/ | python3 -m json.tool; done
    docker-compose down
fi

cd ~
```

---

## ✅ Self-Check

- [ ] Dockerfile likhna aur image build kar sakta/sakti hoon
- [ ] Container run, stop, remove kar sakta/sakti hoon
- [ ] Container ke logs dekh sakta/sakti hoon
- [ ] docker exec se container mein bash open kar sakta/sakti hoon
- [ ] Volume ke saath persistent data store kar sakta/sakti hoon
- [ ] Custom network banana jaanta/jaanti hoon
- [ ] docker-compose.yml likh sakta/sakti hoon
- [ ] docker-compose up/down use kar sakta/sakti hoon

**Next Module:** `09-Cloud-And-Servers` → Cloud deployment! ☁️
