# 🏋️ 09-Cloud-And-Servers: Exercises

---

## Exercise 1: VPS Checklist

```bash
# Ek checklist banao apne future VPS ke liye:
cat > ~/vps_setup_checklist.md << 'EOF'
# VPS Setup Checklist

## Day 1: Basic Setup
- [ ] OS Update (apt-get update && upgrade)
- [ ] Deploy user create karo
- [ ] SSH key authentication setup
- [ ] Root login disable karo
- [ ] UFW firewall enable karo

## Day 2: App Setup
- [ ] Python 3.11+ install karo
- [ ] Nginx install aur configure karo
- [ ] PostgreSQL setup karo
- [ ] Redis setup karo
- [ ] SSL certificate (certbot)

## Day 3: Deployment
- [ ] App code deploy karo (git/rsync)
- [ ] Virtual environment setup
- [ ] Systemd service create karo
- [ ] Nginx virtual host configure karo
- [ ] Test karo!

## Maintenance (Regular)
- [ ] apt-get upgrade (monthly)
- [ ] Logs check karo (weekly)
- [ ] Disk space check (weekly)
- [ ] Backup verify karo (weekly)
EOF

cat ~/vps_setup_checklist.md
```

---

## Exercise 2: AWS S3 Mock (Local)

```bash
# MinIO = Local S3-compatible storage
# (Sirf demo concept ke liye)

cat > s3_operations_demo.py << 'EOF'
import os
import shutil

# Simulate S3 operations locally:
BASE_DIR = "/tmp/fake_s3"

def create_bucket(bucket_name):
    path = os.path.join(BASE_DIR, bucket_name)
    os.makedirs(path, exist_ok=True)
    print(f"Created bucket: {bucket_name}")

def upload_file(local_file, bucket, key):
    dest = os.path.join(BASE_DIR, bucket, key)
    os.makedirs(os.path.dirname(dest), exist_ok=True)
    shutil.copy2(local_file, dest)
    print(f"Uploaded: {local_file} → s3://{bucket}/{key}")

def list_bucket(bucket):
    bucket_path = os.path.join(BASE_DIR, bucket)
    print(f"Contents of s3://{bucket}/:")
    for root, dirs, files in os.walk(bucket_path):
        for f in files:
            filepath = os.path.join(root, f)
            rel_path = os.path.relpath(filepath, bucket_path)
            size = os.path.getsize(filepath)
            print(f"  {rel_path} ({size} bytes)")

# Demo:
create_bucket("my-app-bucket")

# Test file banao:
with open("/tmp/test_upload.txt", "w") as f:
    f.write("Hello from Python!\nThis simulates S3 upload.")

upload_file("/tmp/test_upload.txt", "my-app-bucket", "uploads/test.txt")
upload_file("/tmp/test_upload.txt", "my-app-bucket", "backups/test_backup.txt")

list_bucket("my-app-bucket")

print("\nS3 operations demo complete!")
EOF

python3 s3_operations_demo.py
```

---

## 🔥 Final Project: Complete Deployment Plan

```bash
cat > ~/deployment_plan.md << 'EOF'
# My Flask App Deployment Plan

## Target Architecture
Internet → Cloudflare (DNS + CDN) → Nginx → Gunicorn → Flask → PostgreSQL

## Infrastructure
- VPS: DigitalOcean 1GB Droplet ($6/month)
- OS: Ubuntu 22.04 LTS
- Domain: myapp.com (Namecheap)
- SSL: Let's Encrypt (free)
- Database: PostgreSQL 15 (same server)
- Cache: Redis 7 (same server)

## Application Stack
- Python: 3.11
- Framework: Flask 2.3
- WSGI: Gunicorn (3 workers)
- Process Manager: systemd

## Deployment Steps
1. Droplet create karo
2. Server hardening (SSH keys, UFW, fail2ban)
3. Software install (nginx, python, postgres, redis)
4. App code deploy (git pull)
5. Venv + pip install -r requirements.txt
6. PostgreSQL database + user create
7. .env file setup (prod secrets)
8. Systemd service create + enable
9. Nginx virtual host configure
10. SSL certificate (certbot)
11. Test: curl https://myapp.com

## Monitoring
- journalctl -u myapp -f (live logs)
- nginx logs: /var/log/nginx/
- Server health script: ~/server_health.sh

## Backup Strategy
- Database: pg_dump daily to S3
- Code: GitHub (git push)
- Env file: Encrypted local backup
EOF

cat ~/deployment_plan.md
```

---

## ✅ Final Self-Check

- [ ] VPS setup steps jaanta/jaanti hoon
- [ ] SSH key authentication configure kar sakta/sakti hoon
- [ ] UFW firewall setup kar sakta/sakti hoon
- [ ] Azure ya AWS CLI use kar sakta/sakti hoon
- [ ] Complete Flask deployment plan bana sakta/sakti hoon
- [ ] Server monitoring script likh sakta/sakti hoon

**Projects section pe jao aur real projects complete karo! 🚀**
