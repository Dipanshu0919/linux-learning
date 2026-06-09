# ⚡ 100 Most Useful Linux Commands

> Ye 100 commands ek Linux professional ke daily toolkit mein hote hain!
> Har command practice karo terminal mein.

---

## 🗂️ Navigation (1-10)

| # | Command | Description | Example |
|---|---------|-------------|---------|
| 1 | `pwd` | Current directory | `pwd` |
| 2 | `ls` | List files | `ls -la` |
| 3 | `cd` | Change directory | `cd /var/www` |
| 4 | `cd ~` | Home directory | `cd ~` |
| 5 | `cd -` | Previous dir | `cd -` |
| 6 | `tree` | Directory tree | `tree -L 2` |
| 7 | `find` | Find files | `find . -name "*.py"` |
| 8 | `locate` | Fast search | `locate nginx.conf` |
| 9 | `which` | Command location | `which python3` |
| 10 | `whereis` | Full paths | `whereis nginx` |

---

## 📂 File Operations (11-25)

| # | Command | Description | Example |
|---|---------|-------------|---------|
| 11 | `touch` | Create file | `touch app.py` |
| 12 | `mkdir` | Create directory | `mkdir -p src/app` |
| 13 | `cp` | Copy | `cp -r folder/ backup/` |
| 14 | `mv` | Move/rename | `mv old.py new.py` |
| 15 | `rm` | Delete | `rm -rf folder/` |
| 16 | `ln -s` | Symbolic link | `ln -s /path/to/file link` |
| 17 | `cat` | Print file | `cat requirements.txt` |
| 18 | `less` | Page viewer | `less app.log` |
| 19 | `head` | First lines | `head -20 file.txt` |
| 20 | `tail` | Last lines | `tail -f app.log` |
| 21 | `wc` | Word/line count | `wc -l app.py` |
| 22 | `diff` | Compare files | `diff v1.py v2.py` |
| 23 | `stat` | File metadata | `stat app.py` |
| 24 | `file` | File type | `file image.jpg` |
| 25 | `du` | Disk usage | `du -sh /var/log` |

---

## 🔍 Searching & Text (26-40)

| # | Command | Description | Example |
|---|---------|-------------|---------|
| 26 | `grep` | Text search | `grep -rn "flask" .` |
| 27 | `grep -v` | Invert match | `grep -v "DEBUG" log` |
| 28 | `grep -c` | Count matches | `grep -c "ERROR" log` |
| 29 | `awk` | Text processing | `awk '{print $2}' file` |
| 30 | `sed` | Find & replace | `sed 's/old/new/g' file` |
| 31 | `sort` | Sort lines | `sort -rn numbers.txt` |
| 32 | `uniq` | Unique lines | `sort file \| uniq -c` |
| 33 | `cut` | Extract columns | `cut -d',' -f2 data.csv` |
| 34 | `tr` | Translate chars | `echo "HELLO" \| tr A-Z a-z` |
| 35 | `echo` | Print text | `echo "Hello $USER"` |
| 36 | `printf` | Formatted print | `printf "%s:%d\n" name 42` |
| 37 | `xargs` | Build commands | `find . -name "*.py" \| xargs wc -l` |
| 38 | `tee` | Split output | `cmd \| tee output.txt` |
| 39 | `column` | Format columns | `cat data.txt \| column -t` |
| 40 | `strings` | Extract strings | `strings binary_file` |

---

## 🗜️ Archive & Compression (41-46)

| # | Command | Example |
|---|---------|---------|
| 41 | `tar -cvzf` | `tar -cvzf arch.tar.gz folder/` |
| 42 | `tar -xvzf` | `tar -xvzf arch.tar.gz` |
| 43 | `tar -tvzf` | `tar -tvzf arch.tar.gz` |
| 44 | `zip -r` | `zip -r arch.zip folder/` |
| 45 | `unzip` | `unzip arch.zip -d /tmp/` |
| 46 | `gzip` | `gzip -k file.txt` |

---

## 👤 Users & Permissions (47-58)

| # | Command | Description | Example |
|---|---------|-------------|---------|
| 47 | `whoami` | Current user | `whoami` |
| 48 | `id` | User + groups | `id username` |
| 49 | `who` | Logged in users | `who` |
| 50 | `last` | Login history | `last -10` |
| 51 | `sudo` | Root command | `sudo systemctl restart nginx` |
| 52 | `su` | Switch user | `su - deploy` |
| 53 | `useradd` | Create user | `sudo adduser deploy` |
| 54 | `usermod` | Modify user | `sudo usermod -aG sudo deploy` |
| 55 | `userdel` | Delete user | `sudo userdel -r olduser` |
| 56 | `groupadd` | Create group | `sudo groupadd developers` |
| 57 | `chmod` | Change permissions | `chmod 755 script.sh` |
| 58 | `chown` | Change owner | `sudo chown -R www-data /var/www/` |

---

## ⚙️ Processes (59-67)

| # | Command | Example |
|---|---------|---------|
| 59 | `ps aux` | `ps aux \| grep python` |
| 60 | `top` | `top` (q to quit) |
| 61 | `htop` | `htop` (install: apt install htop) |
| 62 | `kill` | `kill -9 PID` |
| 63 | `pkill` | `pkill gunicorn` |
| 64 | `pgrep` | `pgrep -f "python3 app"` |
| 65 | `nohup` | `nohup python3 app.py &` |
| 66 | `jobs` | `jobs` |
| 67 | `nice` | `nice -n 10 heavy_task.sh` |

---

## 🛠️ System Services (68-73)

| # | Command | Example |
|---|---------|---------|
| 68 | `systemctl start` | `sudo systemctl start nginx` |
| 69 | `systemctl status` | `systemctl status flask_app` |
| 70 | `systemctl enable` | `sudo systemctl enable nginx` |
| 71 | `systemctl reload` | `sudo systemctl reload nginx` |
| 72 | `journalctl` | `journalctl -u nginx -f` |
| 73 | `systemctl list-units` | `systemctl list-units --type=service --state=active` |

---

## 💻 System Info (74-82)

| # | Command | Example |
|---|---------|---------|
| 74 | `uname -a` | Full system info |
| 75 | `df -h` | Disk free space |
| 76 | `free -h` | Memory usage |
| 77 | `uptime` | Load average |
| 78 | `date` | `date "+%Y-%m-%d %H:%M"` |
| 79 | `hostname` | `hostname -I` |
| 80 | `lsb_release -a` | OS version |
| 81 | `nproc` | CPU count |
| 82 | `lscpu` | CPU details |

---

## 🌐 Networking (83-93)

| # | Command | Example |
|---|---------|---------|
| 83 | `ip addr` | `ip a` |
| 84 | `ss -tulpn` | Open ports |
| 85 | `ping` | `ping -c 4 google.com` |
| 86 | `traceroute` | `traceroute google.com` |
| 87 | `curl` | `curl -s https://api.example.com` |
| 88 | `wget` | `wget -q https://example.com/file.zip` |
| 89 | `dig` | `dig +short google.com` |
| 90 | `nslookup` | `nslookup google.com 8.8.8.8` |
| 91 | `ssh` | `ssh -i ~/.ssh/key user@host` |
| 92 | `scp` | `scp file.py user@server:/app/` |
| 93 | `rsync` | `rsync -avz ./ user@server:/app/` |

---

## 📦 Package Management (94-97)

| # | Command | Example |
|---|---------|---------|
| 94 | `apt-get update` | `sudo apt-get update` |
| 95 | `apt-get install` | `sudo apt-get install nginx python3` |
| 96 | `apt-get remove` | `sudo apt-get remove nginx` |
| 97 | `apt list --installed` | `apt list --installed \| grep python` |

---

## 🔧 Miscellaneous Power Tools (98-100)

| # | Command | Description | Example |
|---|---------|-------------|---------|
| 98 | `watch` | Repeat command | `watch -n 2 "ss -tulpn"` |
| 99 | `history` | Command history | `history \| grep git` |
| 100 | `alias` | Create shortcut | `alias ll='ls -la'` |

---

## 🔥 Bonus: Power Combinations

```bash
# Biggest folders in /var:
du -sh /var/* 2>/dev/null | sort -rh | head -10

# Real-time error monitoring:
tail -f /var/log/nginx/error.log | grep --line-buffered "error"

# Find and replace in all Python files:
find . -name "*.py" | xargs sed -i 's/debug=True/debug=False/g'

# Kill all Python processes:
pkill -f python3

# Watch disk usage in real time:
watch -n 5 "df -h /"

# Top 10 IPs in nginx log:
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# All processes using more than 1% CPU:
ps aux | awk '$3 > 1.0 {print $0}'

# Files modified in last 10 minutes:
find . -mmin -10 -type f
```
