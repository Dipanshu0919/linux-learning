# 📁 Lesson 6: SCP & rsync — File Transfer Master

---

## 📚 Learning Objectives

- SCP se files transfer karna
- rsync se efficient sync karna
- SFTP use karna
- Deployment ke liye file transfer

---

## 📤 SCP — Secure Copy

**SCP = SSH + cp = Encrypted file copy**

```bash
# Syntax:
scp [options] source destination

# Local se remote pe:
scp file.txt user@server.com:/home/user/
scp -r folder/ user@server.com:/home/user/     # Folder (-r flag)
scp *.py user@server.com:/var/www/app/         # Multiple files

# Remote se local pe:
scp user@server.com:/var/log/app.log ./        # Download
scp -r user@server.com:/var/www/app/ ./backup/ # Folder download

# Remote se remote pe:
scp user@server1.com:file.txt user@server2.com:/path/

# Custom port:
scp -P 2222 file.txt user@server.com:/path/

# Identity file:
scp -i ~/.ssh/my_key file.txt user@server.com:/path/

# Compression (large files):
scp -C file.txt user@server.com:/path/

# Bandwidth limit (KB/s):
scp -l 1000 file.txt user@server.com:/path/   # 1MB/s limit

# Verbose:
scp -v file.txt user@server.com:/path/
```

---

## 🔄 rsync — Smart Sync (Better than SCP!)

**rsync = Remote Sync = Only changes copy karta hai (Fast!)**

```bash
# Local sync:
rsync -av source/ destination/

# Remote sync:
rsync -av local_folder/ user@server:/remote/folder/
rsync -av user@server:/remote/ ./local_backup/

# Flags:
# -a = Archive mode (permissions, timestamps, links preserve)
# -v = Verbose
# -z = Compress during transfer
# -P = Progress bar + partial (resume support)
# --delete = Destination se extra files delete karo (mirror!)
# -e "ssh -p 2222" = Custom SSH command

# Production deployment:
rsync -avz --delete \
  --exclude '.git/' \
  --exclude '__pycache__/' \
  --exclude '*.pyc' \
  --exclude '.env' \
  ./ deploy@server:/var/www/myapp/

# Dry run (actually nothing copy karo, sirf dekhno):
rsync -avzn source/ destination/    # -n = dry run
```

### rsync vs SCP:
```
SCP:   Always full copy karta hai
rsync: Sirf CHANGED files copy karta hai!

Example: 1GB project, 1 file change
SCP:   1GB transfer (wasteful!)
rsync: Only changed file transfer (seconds!)

Use rsync for:
- Regular deployments
- Backups
- Large file sets
```

---

## 🗄️ SFTP — SSH File Transfer Protocol

```bash
# Connect karo:
sftp user@server.com

# SFTP commands (server pe):
ls                  # Remote directory list
lls                 # LOCAL directory list
pwd                 # Remote current directory
lpwd                # LOCAL current directory
cd /remote/path     # Remote directory change
lcd /local/path     # LOCAL directory change
get file.txt        # Download (remote → local)
put file.txt        # Upload (local → remote)
get -r folder/      # Download folder
put -r folder/      # Upload folder
mkdir newfolder     # Remote folder banao
rm file.txt         # Remote file delete
exit / quit / bye   # Disconnect
```

---

## 🚀 Deployment Workflow (Real World!)

```bash
# Typical Flask app deployment to VPS:

# 1. Requirements freeze karo:
pip freeze > requirements.txt

# 2. .env file exclude karo (sensitive!):
cat > .rsyncignore << 'EOF'
.git/
__pycache__/
*.pyc
.env
venv/
*.sqlite3
EOF

# 3. Code server pe sync karo:
rsync -avz --delete \
  --exclude-from='.rsyncignore' \
  ./ deploy@your-server.com:/var/www/myapp/

# 4. Server pe:
ssh deploy@your-server.com
cd /var/www/myapp

# 5. Virtual env aur packages:
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 6. Service restart:
sudo systemctl restart myapp

# 7. Check:
sudo systemctl status myapp
curl http://localhost:5000
```

---

## 🏋️ Practice

```bash
# 1. SCP practice (localhost pe):
echo "Test file" > /tmp/test_scp.txt
# (Actual server chahiye scp ke liye)

# 2. rsync practice (local directories):
mkdir -p /tmp/source /tmp/destination
echo "File 1" > /tmp/source/file1.txt
echo "File 2" > /tmp/source/file2.txt
mkdir /tmp/source/subdir
echo "Subfile" > /tmp/source/subdir/file3.txt

rsync -av /tmp/source/ /tmp/destination/
ls -la /tmp/destination/

# Change something:
echo "Modified content" > /tmp/source/file1.txt
rsync -av /tmp/source/ /tmp/destination/  # Only file1.txt transfer hoga!

# 3. rsync with exclusions:
rsync -av --exclude="*.txt" /tmp/source/ /tmp/destination2/ 2>/dev/null || mkdir -p /tmp/destination2 && rsync -av --exclude="*.txt" /tmp/source/ /tmp/destination2/
ls /tmp/destination2/
```

---

## 📖 Summary

```
scp file user@srv:/path     = Upload file
scp user@srv:/path file     = Download file
scp -r folder user@srv:/    = Upload folder
rsync -av src/ dst/         = Smart sync
rsync -avz src/ user@srv:dst/ = Remote sync
rsync --delete              = Mirror (delete extra)
rsync -n                    = Dry run (test)
sftp user@server            = Interactive file transfer
```

---

**Next:** `05-Networking/Exercises.md` → Network master karo! 🌐
