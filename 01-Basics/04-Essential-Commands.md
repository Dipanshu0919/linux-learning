# ⚡ Lesson 4: Essential Commands — Tumhara Daily Toolkit

---

## 📚 Learning Objectives

- 25+ essential Linux commands
- File aur directory operations master karna
- Text manipulation basics
- System information commands
- Real-world use cases samjhna

---

## 📁 File & Directory Commands

### `mkdir` — Directory banao

```bash
# Syntax:
mkdir directory_name

# Examples:
mkdir my_project             # Single folder
mkdir -p projects/web/flask  # Nested folders (parent bhi banaata hai!)
mkdir folder1 folder2 folder3 # Multiple folders ek saath

# With specific permissions:
mkdir -m 755 new_folder
```

**Real-world use:**
```bash
# Python project structure banana:
mkdir -p my_flask_app/{templates,static,tests}
ls my_flask_app/
# templates  static  tests
```

---

### `touch` — File banao ya timestamp update karo

```bash
# Syntax:
touch filename

# Examples:
touch notes.txt              # Empty file banao
touch app.py styles.css      # Multiple files
touch {file1,file2,file3}.txt # Brace expansion — 3 files ek saath!

# Timestamp update karo (file already exist karti ho):
touch existing_file.txt      # Modification time update hogi
```

**Python developer ke liye:**
```bash
# Project structure setup:
touch app.py requirements.txt README.md .env
```

---

### `cp` — Copy karo

```bash
# Syntax:
cp source destination

# File copy:
cp file.txt copy_of_file.txt

# Folder ke andar copy:
cp file.txt /tmp/

# Folder copy karna (-r = recursive):
cp -r my_folder/ backup_folder/

# Preserve permissions aur timestamps:
cp -p file.txt file_backup.txt

# Interactive (overwrite se pehle puchega):
cp -i file.txt existing_file.txt

# Verbose (kya ho raha hai dikhao):
cp -v file.txt /tmp/
# Output: 'file.txt' -> '/tmp/file.txt'
```

---

### `mv` — Move ya Rename karo

```bash
# Syntax:
mv source destination

# File rename karna:
mv old_name.txt new_name.txt

# File move karna:
mv file.txt /tmp/file.txt
# Ya shorthand:
mv file.txt /tmp/

# Folder move:
mv my_folder/ /home/codespace/

# Interactive (overwrite se pehle puchega):
mv -i file.txt existing_file.txt
```

**Important:** `mv` = Cut + Paste. Original wahan nahi rahega!

---

### `rm` — Delete karo

```bash
# Syntax:
rm [options] file

# File delete:
rm file.txt

# Multiple files:
rm file1.txt file2.txt file3.txt

# Interactive (har file ke liye puchega):
rm -i file.txt

# Folder delete karna (CAREFUL!):
rm -r folder_name/

# Force delete (no warning!):
rm -f file.txt

# Folder forcefully delete:
rm -rf folder_name/   # BAHUT CAREFUL! Undo nahi hota!

# Verbose output:
rm -v file.txt
# Output: removed 'file.txt'
```

### ⚠️ DANGER ZONE — Kabhi Mat Karo:
```bash
rm -rf /         # SYSTEM DELETE HO JAAYEGA!
rm -rf /*        # SAME! System wipe!
rm -rf ~         # Home folder delete!
```

**Golden Rule:** `rm` se pehle `ls` se confirm karo kya delete hona hai!

---

### `cat` — File content dekho / Files combine karo

```bash
# Syntax:
cat [options] file

# File content print karo:
cat README.md

# Multiple files combine karke print karo:
cat file1.txt file2.txt

# Line numbers ke saath:
cat -n file.txt

# File mein text likhna (new file):
cat > new_file.txt << EOF
Ye pehli line hai
Ye doosri line hai
EOF

# File mein append karna:
cat >> existing_file.txt << EOF
Ye naya content add ho gaya
EOF
```

**Python developer ke liye:**
```bash
# Requirements.txt check karo:
cat requirements.txt

# Python file padho:
cat app.py
```

---

### `wc` — Word Count / Line Count

```bash
# Lines count:
wc -l file.txt

# Words count:
wc -w file.txt

# Characters count:
wc -c file.txt

# All info:
wc file.txt
# Output: 50  200  1024  file.txt
#         |    |    |      |
#        lines words chars filename
```

**Real use:**
```bash
# Kitni Python files hain project mein?
find . -name "*.py" | wc -l

# Log file mein kitni ERROR lines hain?
grep "ERROR" app.log | wc -l
```

---

## 🔍 Viewing & Reading Commands

### `less` — File page by page dekhna

```bash
# Syntax:
less filename

less README.md
less /var/log/syslog
```

**less ke shortcuts:**
```
Space / f = Next page
b         = Previous page
j / Down  = One line down
k / Up    = One line up
/word     = Search (n = next match, N = previous)
G         = End of file
g         = Beginning of file
q         = Quit
```

---

### `head` aur `tail` — File ke part dekhna

```bash
# First 10 lines (default):
head file.txt

# First 20 lines:
head -20 file.txt

# Last 10 lines:
tail file.txt

# Last 20 lines:
tail -20 file.txt

# MOST USEFUL: Live file monitoring!
tail -f /var/log/syslog
# Ctrl+C se band karo
```

**Real-world use:**
```bash
# Flask app ke live logs dekhna:
tail -f app.log

# Last error message:
tail -1 error.log

# Config file ka pehla hissa:
head -50 /etc/nginx/nginx.conf
```

---

## 🔎 Searching Commands

### `grep` — Text search karna

```bash
# Syntax:
grep "pattern" file

# File mein text dhundna:
grep "ERROR" app.log

# Case insensitive:
grep -i "error" app.log

# Line number ke saath:
grep -n "def" app.py

# Recursive (folders mein bhi):
grep -r "import flask" .

# Matching nahi hone wali lines:
grep -v "DEBUG" app.log

# Count matching lines:
grep -c "ERROR" app.log

# Multiple files:
grep "TODO" *.py
```

**Python developer ke liye:**
```bash
# Project mein kahan Flask import hua hai?
grep -rn "from flask import" .

# Kahan koi function define hua hai?
grep -n "def " app.py

# Requirements mein specific package:
grep "flask" requirements.txt
```

---

### `find` — Files dhundna

```bash
# Syntax:
find [path] [options]

# Current folder mein naam se:
find . -name "app.py"

# Extension se:
find . -name "*.py"

# Case insensitive:
find . -iname "*.PY"

# Type se (f=file, d=directory):
find . -type f -name "*.txt"
find . -type d -name "templates"

# Size se:
find . -size +1M    # 1MB se bada
find . -size -100k  # 100KB se chhota

# Modified time se:
find . -mtime -1    # Last 24 hours mein modified
find . -mtime +7    # 7 din se zyada purani

# Permission se:
find . -perm 644
```

---

## ℹ️ System Information Commands

### `uname` — System info

```bash
uname -a          # Full info
uname -r          # Kernel version
uname -m          # Architecture (x86_64, arm64)
```

### `whoami` — Current user

```bash
whoami
# Output: codespace
```

### `id` — User aur group info

```bash
id
# Output: uid=1000(codespace) gid=1000(codespace) groups=1000(codespace),4(adm),27(sudo)
```

### `hostname` — Computer ka naam

```bash
hostname
# Output: my-server-name
```

### `uptime` — Kitni der se chala hai?

```bash
uptime
# Output: 10:30:00 up 2 days, 3:45, 1 user, load average: 0.12, 0.08, 0.05
```

### `date` — Date aur time

```bash
date                              # Current date/time
date "+%Y-%m-%d"                  # Sirf date (2026-06-07)
date "+%H:%M:%S"                  # Sirf time (10:30:00)
date "+%Y-%m-%d_%H:%M:%S"         # Timestamp
```

---

## 💾 Disk & Memory Commands

### `df` — Disk Free (Disk space check)

```bash
df -h          # Human readable (GB, MB)
df -h /home    # Specific partition
```

Output:
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  8.5G   20G  30% /
tmpfs           7.8G     0  7.8G   0% /dev/shm
```

### `du` — Disk Usage (Folder ka size)

```bash
du -h folder/           # Folder ka size
du -sh folder/          # Summary (single line)
du -sh *                # Current dir ke sab items
du -sh * | sort -h      # Size se sort karo
```

**Real use:**
```bash
# Kaun sa folder zyada jagah le raha hai?
du -sh * | sort -rh | head -10
```

### `free` — RAM check

```bash
free -h
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:           15Gi       4.2Gi       8.3Gi       234Mi       3.1Gi       10.7Gi
Swap:         2.0Gi          0B       2.0Gi
```

---

## 🔗 Useful Operators

### `|` (Pipe) — Ek command ka output doosri command mein bhejo

```bash
ls -la | grep ".py"           # Python files list
cat app.log | grep "ERROR"    # Error lines
history | grep "git"          # Git commands history
ps aux | grep "python"        # Python processes
```

### `>` — Output file mein save karo (overwrite)

```bash
ls > file_list.txt           # ls output save karo
echo "Hello" > hello.txt     # Text file banao
```

### `>>` — Output file mein append karo

```bash
echo "Line 1" > file.txt
echo "Line 2" >> file.txt    # Line 1 ke baad add hogi
cat file.txt
# Line 1
# Line 2
```

### `&&` — Dono commands chalao (agar pehli successful ho)

```bash
mkdir new_project && cd new_project && touch app.py
# Pehle folder banao, phir usme jao, phir file banao
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: Project structure banao
mkdir -p my_project/{src,tests,docs}
touch my_project/src/main.py
touch my_project/requirements.txt
ls -la my_project/
ls -la my_project/src/

# Task 2: File operations
echo "# My First Linux Project" > my_project/README.md
echo "flask==2.3.0" > my_project/requirements.txt
echo "requests==2.28.0" >> my_project/requirements.txt
cat my_project/requirements.txt

# Task 3: Searching
grep "flask" my_project/requirements.txt
find my_project -name "*.py"
find my_project -name "*.md"

# Task 4: System check
df -h
free -h
uptime
whoami
date "+%Y-%m-%d %H:%M:%S"

# Task 5: Cleanup practice
rm my_project/src/main.py
# Verify:
ls my_project/src/
# Empty!
```

---

## 📖 Summary

```
mkdir     = Folder banao
touch     = Empty file banao
cp        = Copy karo
mv        = Move ya rename karo
rm        = Delete karo (CAREFUL!)
cat       = File content dekho
head      = First lines dekho
tail      = Last lines dekho (-f for live)
grep      = Text search karo
find      = Files dhundho
df -h     = Disk space
free -h   = RAM check
|         = Pipe (output pipe karo)
>         = Overwrite file mein save
>>        = Append file mein
&&        = Dono commands chalao
```

---

**Next:** `01-Basics/Exercises.md` → Ab practice karo! 💪
