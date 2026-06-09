# 📁 Lesson 3: Linux Filesystem — Map of the System

---

## 📚 Learning Objectives

- Linux ka folder structure samjhna
- Important system folders kahan hote hain
- Absolute aur Relative paths ka difference
- Home directory kya hai
- Python developer ke liye kaunse folders important hain

---

## 🗺️ Linux Filesystem — Big Picture

Linux ka poora system ek **tree** ki tarah hota hai — sab kuch **root** (`/`) se start hota hai.

```
/  (Root — sab kuch yahan se start)
|
|-- bin/      Essential user commands (ls, cp, mv, etc.)
|-- boot/     Bootloader files
|-- dev/      Device files (hard disk, USB, etc.)
|-- etc/      Configuration files (IMPORTANT!)
|-- home/     User home directories
|   |-- codespace/   <- Tumhara home folder
|       |-- linux-learning/
|       |-- projects/
|
|-- lib/      System libraries
|-- media/    Mounted drives (USB, CD)
|-- mnt/      Temporarily mounted filesystems
|-- opt/      Optional software
|-- proc/     Running processes info
|-- root/     Root user ka home
|-- run/      Runtime data
|-- srv/      Service data
|-- sys/      System/hardware info
|-- tmp/      Temporary files (reboot pe delete hote hain!)
|-- usr/      User programs aur libraries
|   |-- bin/  Most user commands yahan hain
|   |-- lib/  Libraries
|   +-- local/ Manually installed software
|
+-- var/      Variable data (logs, databases)
    |-- log/  Log files (IMPORTANT!)
    +-- www/  Web files
```

---

## 📂 Important Folders — Kaan Kaan Janna Zaroor Hai

### 1. `/` — Root Directory

```bash
ls /
```

```
bin  boot  dev  etc  home  lib  media  mnt  opt  proc  root  run  srv  sys  tmp  usr  var
```

Ye sab kuch ka starting point hai. Windows mein C:\ jaisa!

---

### 2. `/home` — User Home Directories

```bash
ls /home
# Output: codespace  (ya tumhara username)

ls /home/codespace
# Tumhara personal folder!
```

**Shortcut:** `~` matlab `/home/YOUR_USERNAME`

```bash
cd ~              # Home folder mein jao
echo $HOME        # Home path print karo
# Output: /home/codespace
```

**Python developer note:** Apne projects yahan banao!
```
/home/codespace/
    linux-learning/
    my-flask-app/
    my-fastapi-project/
```

---

### 3. `/etc` — Configuration Files

Ye **sab software ki settings** ka folder hai!

```bash
ls /etc
```

```
Important files in /etc:
/etc/hosts          -- IP to hostname mapping
/etc/hostname       -- Server ka naam
/etc/passwd         -- User accounts list
/etc/group          -- Groups list
/etc/ssh/           -- SSH settings
/etc/nginx/         -- Nginx web server settings
/etc/apt/           -- Package manager settings
/etc/crontab        -- Scheduled tasks
```

```bash
# Dekho tumhara hostname kya hai
cat /etc/hostname

# Dekho /etc/hosts file
cat /etc/hosts

# Tumhare system pe kaunse users hain
cat /etc/passwd
```

---

### 4. `/var` — Variable Data

```bash
ls /var
```

```
Important:
/var/log/          -- LOG FILES! Debugging ka best friend
/var/www/          -- Web server files (default)
/var/lib/          -- Application data
/var/tmp/          -- Temporary files (reboot pe bhi rehti hain)
```

**Log files dekhna:**
```bash
ls /var/log
cat /var/log/syslog   # System log
# Ya tail command se live logs:
tail -f /var/log/syslog
```

---

### 5. `/tmp` — Temporary Files

```bash
ls /tmp
```

Koi bhi file yahan rakh sakte ho — **reboot pe delete ho jaati hain!**

```bash
# Testing ke liye:
touch /tmp/test_file.txt
ls /tmp
```

---

### 6. `/usr/bin` vs `/bin` — Programs kahan hain?

```bash
# Commands yahan hote hain
ls /usr/bin | head -20
ls /bin

# Which command kahan hai?
which python3
# Output: /usr/bin/python3

which ls
# Output: /usr/bin/ls
```

---

### 7. `/proc` — Running System Info

```bash
# Ye virtual filesystem hai — RAM mein hota hai!

# CPU info
cat /proc/cpuinfo

# Memory info  
cat /proc/meminfo

# Running processes
ls /proc/        # Numbers = Process IDs!
```

---

## 🔗 Absolute vs Relative Path

Ye concept bahut important hai!

### Absolute Path:
- `/` se start hota hai
- Kahan bhi ho, ye hamesha kaam karta hai

```bash
cd /home/codespace/linux-learning  # Absolute path
cat /etc/hostname                   # Absolute path
```

### Relative Path:
- Current folder se relative hota hai
- Short aur convenient

```bash
# Agar tum /home/codespace mein ho:
cd linux-learning           # Relative path
cd ./linux-learning         # Same thing (. = current dir)
cd ../                      # Parent directory
cd ../../                   # Two levels up
```

### Visual Example:
```
Current location: /home/codespace/linux-learning/01-Basics/

Absolute: /home/codespace/linux-learning/README.md
Relative: ../README.md                              (ek level up, phir README.md)
```

---

## 📌 Special Symbols

| Symbol | Matlab | Example |
|--------|--------|---------|
| `.` | Current directory | `ls .` ya sirf `ls` |
| `..` | Parent directory | `cd ..` |
| `~` | Home directory | `cd ~` |
| `/` | Root directory | `cd /` |
| `-` | Previous directory | `cd -` |

---

## 🔍 File Types in Linux

```bash
ls -la
```

Output mein pehla character file type batata hai:

```
-  = Regular file          (-rw-r--r-- README.md)
d  = Directory             (drwxr-xr-x 01-Basics/)
l  = Symbolic link (shortcut) (lrwxrwxrwx python -> python3)
c  = Character device
b  = Block device
p  = Named pipe
s  = Socket
```

```bash
# File type check karo
file README.md
# Output: README.md: ASCII text

file /usr/bin/python3
# Output: /usr/bin/python3: ELF 64-bit LSB executable...

file /tmp
# Output: /tmp: directory
```

---

## 🐍 Python Developer ke Liye Important Paths

```bash
# Python kahan hai?
which python3
# /usr/bin/python3

# Pip kahan hai?
which pip3
# /usr/bin/pip3

# Python packages kahan install hoti hain?
python3 -c "import site; print(site.getsitepackages())"
# ['/usr/local/lib/python3.10/dist-packages', '/usr/lib/python3/dist-packages']

# Virtual environment mein packages:
# /home/codespace/myproject/venv/lib/python3.10/site-packages/

# Environment variables kahan configure hain:
cat ~/.bashrc           # Bash configuration
cat ~/.bash_profile     # Login shell configuration
```

---

## 🔧 Troubleshooting

### "No such file or directory"
```bash
cd linux-learing   # Galat spelling!
# Error: No such file or directory

# Solution: Tab completion use karo ya spelling check karo
cd linux-learning  # SAHI
```

### "Permission denied"
```bash
cat /root/.bashrc
# Error: Permission denied

# Solution: sudo use karo (if needed)
sudo cat /root/.bashrc
```

### Ghalat folder mein ho?
```bash
pwd              # Check karo kahan ho
ls               # Kya hai yahan?
cd ~             # Home folder mein jao (safe!)
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: Root directory explore karo
ls /
cd /
ls

# Task 2: Home folder explore karo
cd ~
ls -la

# Task 3: Important config files dekho
cat /etc/hostname
cat /etc/os-release

# Task 4: System info
cat /proc/cpuinfo | head -20
cat /proc/meminfo | head -10

# Task 5: Absolute vs Relative practice
cd /home
pwd
cd codespace      # Relative path (ek folder)
pwd
cd ../..          # Root pe jao
pwd
cd ~              # Home wapas
pwd
```

---

## 📖 Summary

```
/         = Root — sab kuch yahan se
/home     = User folders
/etc      = Configuration files
/var/log  = Log files (debugging)
/tmp      = Temporary files
/usr/bin  = Most programs/commands
~         = Tumhara home folder
.         = Current directory
..        = Parent directory
-         = Previous directory
```

---

**Next Lesson:** `04-Essential-Commands.md` → Sab important commands seekho! ⚡
