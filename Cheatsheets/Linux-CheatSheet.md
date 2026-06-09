# 🐧 Linux Command Cheatsheet — Quick Reference

---

## 📁 Navigation

| Command | Description | Example |
|---------|-------------|---------|
| `pwd` | Current directory | `pwd` |
| `ls` | List files | `ls -la` |
| `cd dir` | Change directory | `cd /var/www` |
| `cd ~` | Home directory | `cd ~` |
| `cd -` | Previous directory | `cd -` |
| `cd ..` | Parent directory | `cd ..` |

---

## 📂 File Operations

| Command | Description | Example |
|---------|-------------|---------|
| `touch file` | Create empty file | `touch app.py` |
| `mkdir dir` | Create directory | `mkdir -p src/app` |
| `cp src dst` | Copy | `cp file.py backup.py` |
| `mv src dst` | Move/Rename | `mv old.py new.py` |
| `rm file` | Delete file | `rm -rf folder/` |
| `cat file` | Print file | `cat app.py` |
| `less file` | Page viewer | `less big_log.txt` |
| `head -N` | First N lines | `head -20 file.txt` |
| `tail -N` | Last N lines | `tail -f app.log` |
| `wc -l` | Line count | `wc -l app.py` |

---

## 🔍 Search

| Command | Description | Example |
|---------|-------------|---------|
| `grep "pat" file` | Search in file | `grep "ERROR" app.log` |
| `grep -r "pat" .` | Recursive search | `grep -rn "flask" .` |
| `grep -v "pat"` | Invert match | `grep -v "DEBUG" log` |
| `find . -name "*.py"` | Find files | `find . -type f -name "*.py"` |
| `find . -mtime -1` | Modified today | `find /tmp -mtime -1` |
| `which cmd` | Command location | `which python3` |

---

## 👤 Users & Permissions

| Command | Description | Example |
|---------|-------------|---------|
| `whoami` | Current user | `whoami` |
| `id` | User + groups | `id` |
| `sudo cmd` | Run as root | `sudo apt install nginx` |
| `chmod 755 file` | Set permissions | `chmod 644 app.py` |
| `chown user:grp` | Change owner | `chown www-data:www-data /var/www/` |
| `useradd -m user` | Create user | `sudo adduser deploy` |

**Permissions Quick Reference:**
```
7 = rwx (read + write + execute)
6 = rw- (read + write)
5 = r-x (read + execute)
4 = r-- (read only)
0 = --- (none)

755 = rwxr-xr-x  (dirs, executables)
644 = rw-r--r--  (regular files)
600 = rw-------  (private, .env, keys)
```

---

## ⚙️ Processes & Services

| Command | Description | Example |
|---------|-------------|---------|
| `ps aux` | All processes | `ps aux | grep python` |
| `top` | Live processes | `top` |
| `kill PID` | Kill process | `kill -9 1234` |
| `pkill name` | Kill by name | `pkill gunicorn` |
| `pgrep name` | Find PID | `pgrep nginx` |
| `cmd &` | Background | `python3 app.py &` |
| `jobs` | Background jobs | `jobs` |
| `fg N` | Foreground | `fg 1` |
| `nohup cmd &` | Persistent BG | `nohup gunicorn app:app &` |
| `systemctl status` | Service status | `systemctl status nginx` |
| `systemctl start` | Start service | `sudo systemctl start nginx` |
| `systemctl restart` | Restart | `sudo systemctl restart flask_app` |
| `systemctl enable` | Boot autostart | `sudo systemctl enable nginx` |

---

## 💻 System Info

| Command | Description | Example |
|---------|-------------|---------|
| `uname -a` | System info | `uname -a` |
| `df -h` | Disk usage | `df -h /` |
| `free -h` | Memory | `free -h` |
| `du -sh dir` | Folder size | `du -sh /var/log` |
| `uptime` | Load average | `uptime` |
| `hostname` | Hostname | `hostname -I` |
| `date` | Date/Time | `date "+%Y-%m-%d"` |

---

## 🌐 Networking

| Command | Description | Example |
|---------|-------------|---------|
| `ip addr` | IP addresses | `ip a` |
| `ss -tulpn` | Open ports | `ss -tulpn | grep :80` |
| `ping host` | Connectivity | `ping -c 4 google.com` |
| `curl URL` | HTTP request | `curl -s http://localhost:5000` |
| `wget URL` | Download | `wget -q http://example.com/file` |
| `ssh user@host` | Remote login | `ssh deploy@192.168.1.100` |
| `scp src dst` | Secure copy | `scp app.py user@server:/app/` |
| `rsync -avz` | Smart sync | `rsync -avz ./ user@srv:/app/` |

---

## 📝 Text Processing

| Command | Description | Example |
|---------|-------------|---------|
| `echo "text"` | Print text | `echo $HOME` |
| `sort file` | Sort lines | `sort -rn numbers.txt` |
| `uniq` | Remove duplicates | `sort file | uniq -c` |
| `cut -d',' -f2` | Extract column | `cut -d',' -f1 data.csv` |
| `sed 's/a/b/g'` | Find & replace | `sed -i 's/old/new/g' file` |
| `awk '{print $2}'` | Column extract | `awk -F',' '{print $1}' data` |
| `diff f1 f2` | Compare files | `diff v1.py v2.py` |

---

## 🗜️ Compression

| Command | Description |
|---------|-------------|
| `tar -cvzf arch.tar.gz dir/` | Create compressed archive |
| `tar -xvzf arch.tar.gz` | Extract archive |
| `tar -tvzf arch.tar.gz` | List contents |
| `zip -r arch.zip dir/` | Create zip |
| `unzip arch.zip` | Extract zip |
| `gzip file` | Compress single file |

---

## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Stop current command |
| `Ctrl+Z` | Suspend (background) |
| `Ctrl+L` | Clear terminal |
| `Ctrl+R` | History search |
| `Ctrl+A` | Beginning of line |
| `Ctrl+E` | End of line |
| `Tab` | Autocomplete |
| `!!` | Repeat last command |
| `!grep` | Repeat last grep |

---

## 📋 Pipe & Redirection

```bash
cmd1 | cmd2          # Pipe output to next command
cmd > file           # Overwrite output to file
cmd >> file          # Append output to file
cmd 2> error.log     # Redirect stderr
cmd &> all.log       # Redirect stdout + stderr
cmd1 && cmd2         # Run cmd2 if cmd1 succeeds
cmd1 || cmd2         # Run cmd2 if cmd1 fails
```

---

## 🔑 SSH Quick Reference

```bash
ssh user@host              # Connect
ssh -p 2222 user@host      # Custom port
ssh -i key.pem user@host   # Specific key
ssh-keygen -t ed25519      # Generate key pair
ssh-copy-id user@host      # Copy key to server
cat ~/.ssh/id_ed25519.pub  # Your public key
```
