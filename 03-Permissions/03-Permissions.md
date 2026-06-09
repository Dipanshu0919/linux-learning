# 🔐 Lesson 3: Permissions — Linux Security System

---

## 📚 Learning Objectives

- Linux permission system samjhna (rwx)
- chmod se permissions change karna
- chown se ownership change karna
- Octal notation samjhna
- Special permissions (SUID, SGID, Sticky bit)

---

## 🔐 Linux Permission System — Overview

Har file aur folder ke teeen sets of permissions hote hain:

```
-rwxr-xr--  1  owner  group  size  date  filename
|  | |  |
|  | |  +-- Others (baaki sab)
|  | +-- Group
|  +-- Owner (File ka malik)
+-- File type (- = file, d = directory, l = link)
```

### Permission Characters:
```
r = Read     (4)  = File padh sako
w = Write    (2)  = File modify kar sako
x = Execute  (1)  = File run kar sako / folder mein enter kar sako
- = No permission  = Allowed nahi
```

### Visual Breakdown:
```
ls -la app.py

-rwxr-xr--
 ^^^         Owner permissions: rwx (read + write + execute = 7)
    ^^^      Group permissions: r-x (read + execute = 5)
       ^^^   Others permissions: r-- (read only = 4)
```

---

## 🔢 Octal Notation (Numbers se permissions)

```
r = 4
w = 2
x = 1
- = 0

Examples:
rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
--- = 0+0+0 = 0

Common permission sets:
755 = rwxr-xr-x  (folders aur executables ke liye)
644 = rw-r--r--  (regular files ke liye)
600 = rw-------  (private files ke liye)
777 = rwxrwxrwx  (DANGEROUS! Sab kuch allowed)
```

---

## 🛠️ `chmod` — Permissions Change Karo

### Symbolic Method:
```bash
chmod [who][operator][permission] file

# who:
# u = user/owner
# g = group
# o = others
# a = all (u+g+o)

# operator:
# + = permission add karo
# - = permission remove karo
# = = exactly ye permissions set karo

# Examples:
chmod u+x script.sh          # Owner ko execute permission do
chmod g-w file.txt           # Group se write permission hato
chmod o+r file.txt           # Others ko read permission do
chmod a+r file.txt           # Sab ko read permission do
chmod u+x,g-w file.txt       # Multiple changes ek saath
chmod a=r file.txt           # Sab ke liye sirf read
```

### Octal Method (Recommended!):
```bash
# Syntax:
chmod NNN file.txt

# Common examples:
chmod 755 script.sh     # -rwxr-xr-x  (executable script)
chmod 644 config.txt    # -rw-r--r--  (regular file)
chmod 600 .ssh/id_rsa   # -rw-------  (private key!)
chmod 700 private_dir/  # drwx------  (private folder)
chmod 777 public/       # AVOID! Security risk!

# Recursive (folder + contents):
chmod -R 755 my_project/
chmod -R 644 my_project/static/

# Python project typical setup:
chmod 755 run.sh           # Script executable
chmod 644 app.py           # Python file readable
chmod 600 .env             # Private env file!
chmod -R 755 my_flask_app/ # Whole project
```

---

## 👑 `chown` — Ownership Change Karo

```bash
# Syntax:
chown owner:group file

# Owner change:
sudo chown alice file.txt

# Owner + group change:
sudo chown alice:developers file.txt

# Sirf group change:
sudo chown :developers file.txt
# Ya:
sudo chgrp developers file.txt

# Recursive:
sudo chown -R alice:developers project/

# Server pe typical use:
sudo chown -R www-data:www-data /var/www/html/  # Nginx web files
sudo chown -R codespace:codespace ~/projects/    # Dev files
```

---

## 🔍 Permission Check Karna

```bash
# File permissions:
ls -la filename.txt

# Directory permissions:
ls -ld directory/

# Access test:
# Can I read?
[ -r file.txt ] && echo "Readable" || echo "Not readable"

# Can I write?
[ -w file.txt ] && echo "Writable" || echo "Not writable"

# Can I execute?
[ -x script.sh ] && echo "Executable" || echo "Not executable"
```

---

## 🛡️ Special Permissions

### SUID (Set User ID) — 4xxx
```bash
# Program owner ke permissions se run hota hai
chmod u+s script        # Symbolic
chmod 4755 script       # Octal

# Example:
ls -la /usr/bin/passwd
# -rwsr-xr-x (s in owner execute = SUID)
# passwd root ke permissions se run hota hai!
```

### SGID (Set Group ID) — 2xxx
```bash
chmod g+s directory/    # Folder mein create ki gayi files group inherit karengi
chmod 2755 directory/
```

### Sticky Bit — 1xxx
```bash
chmod +t /tmp           # Sirf owner apni file delete kar sakta hai
chmod 1777 /tmp         # /tmp pe already hai!

ls -la /
# drwxrwxrwt ... tmp    (t at end = sticky bit)
```

---

## 🔒 Security Best Practices

```bash
# Private keys:
chmod 600 ~/.ssh/id_rsa    # Sirf owner read/write

# SSH authorized_keys:
chmod 644 ~/.ssh/authorized_keys

# SSH directory:
chmod 700 ~/.ssh/

# .env file (secrets!):
chmod 600 .env

# Web server files:
chmod 755 /var/www/html/      # Directory
chmod 644 /var/www/html/*.html # Files

# Python scripts:
chmod 755 run_app.sh    # Executable
chmod 644 app.py        # Non-executable
```

---

## 🏋️ Practice

```bash
# Setup:
mkdir permission_practice
cd permission_practice
touch public.txt private.txt secret.txt script.sh

# Check initial permissions:
ls -la

# Set permissions:
chmod 644 public.txt    # Standard file
chmod 600 private.txt   # Private
chmod 400 secret.txt    # Read-only (even for owner!)
chmod 755 script.sh     # Executable

ls -la

# Test permissions:
cat public.txt   # Works
cat secret.txt   # Works (read-only, not no-read)

echo "Try write" >> secret.txt  # FAILS! Permission denied

# Script test:
echo "echo 'Script running!'" > script.sh
./script.sh     # Should run!

chmod 644 script.sh  # Remove execute
./script.sh     # FAILS! Permission denied

chmod 755 script.sh  # Add execute back
./script.sh     # Works again!

cd ..
```

---

## 📖 Summary

```
ls -la           = Permissions dekho
chmod 755 file   = Permissions set karo (numeric)
chmod u+x file   = Symbolic method
chmod -R 644 dir = Recursive
chown user file  = Owner change
chown user:grp   = Owner + group change
r=4, w=2, x=1   = Octal values
755 = rwxr-xr-x  = Standard executable
644 = rw-r--r--  = Standard file
600 = rw-------  = Private file
```

---

**Next:** `03-Permissions/04-Sudo.md` → Sudo master karo! 🔑
