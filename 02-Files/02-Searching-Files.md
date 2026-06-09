# 🔍 Lesson 2: Searching Files — Kuch bhi Dhundho!

---

## 📚 Learning Objectives

- `find` command master karna
- `grep` se text search karna
- `locate` command use karna
- `which` aur `whereis` use karna
- Complex search queries banana

---

## 🔍 `find` — File System Search

`find` sabse powerful search tool hai Linux mein!

### Basic Syntax:
```bash
find [path] [conditions] [actions]
```

### By Name:
```bash
find . -name "app.py"              # Exact naam
find . -name "*.py"                # Pattern se
find . -iname "*.PY"               # Case insensitive
find /home -name "requirements.txt" # Specific path mein
```

### By Type:
```bash
find . -type f         # Sirf files
find . -type d         # Sirf directories
find . -type l         # Sirf symbolic links
```

### By Size:
```bash
find . -size +1M       # 1 MB se bade files
find . -size -100k     # 100 KB se chhote
find . -size +1M -size -100M  # 1MB aur 100MB ke beech

# Size units:
# c = bytes
# k = kilobytes
# M = megabytes
# G = gigabytes
```

### By Time:
```bash
find . -mtime -1       # Last 24 hours mein modified
find . -mtime +7       # 7 din se purani
find . -mtime 1        # Exactly 1 din purani
find . -mmin -30       # Last 30 minutes mein
find . -newer file.txt # file.txt se newer
```

### By Permissions:
```bash
find . -perm 644       # Exactly 644
find . -perm -644      # At least 644 (includes 755, etc.)
find . -perm /111      # Execute bit set ho kisi bhi user ke liye
```

### By Owner:
```bash
find . -user codespace  # Specific user ki files
find . -group users     # Specific group ki files
```

### find + Actions:
```bash
# Find karke delete karo:
find . -name "*.tmp" -delete

# Find karke command run karo:
find . -name "*.py" -exec python3 -m py_compile {} \;
# {} = found file ka placeholder
# \; = command end

# Find karke copy karo:
find . -name "*.py" -exec cp {} /backup/ \;

# Find karke permissions set karo:
find . -type f -exec chmod 644 {} \;
find . -type d -exec chmod 755 {} \;

# Find karke print karo:
find . -name "*.log" -print
```

### Multiple Conditions:
```bash
# AND (default):
find . -name "*.py" -type f -size +1k

# OR:
find . -name "*.py" -o -name "*.js"

# NOT:
find . -not -name "*.txt"
find . ! -name "*.txt"   # Same thing
```

### Real-world Examples:
```bash
# Project mein sab Python files:
find ~/projects -name "*.py" -type f

# Log files last week ki:
find /var/log -mtime -7 -name "*.log"

# Empty files delete karo:
find . -empty -type f -delete

# Large files dhundho:
find / -size +500M -type f 2>/dev/null

# Broken symbolic links:
find . -type l -! -e

# Files without owner:
find . -nouser
```

---

## 📝 `grep` — Text Search Master

### Basic Syntax:
```bash
grep [options] "pattern" file
```

### Basic Usage:
```bash
grep "flask" requirements.txt
grep "def " app.py
grep "ERROR" /var/log/syslog
```

### Options:
```bash
grep -i "error" log.txt        # Case insensitive
grep -n "def " app.py          # Line numbers ke saath
grep -r "import" .             # Recursive (folders mein)
grep -l "flask" *.py           # Sirf file names (content nahi)
grep -v "DEBUG" app.log        # Inverted (match NAHI hone wali lines)
grep -c "ERROR" app.log        # Count matching lines
grep -w "word" file.txt        # Whole word match
grep -A 3 "ERROR" app.log      # Match ke baad 3 lines
grep -B 3 "ERROR" app.log      # Match se pehle 3 lines
grep -C 3 "ERROR" app.log      # Match ke upar-niche 3 lines
```

### Regular Expressions with grep:
```bash
# Any character:
grep "f.sk" file.txt           # flask, fask, f5sk, etc.

# Start of line:
grep "^def" app.py             # "def" se shuru hone wali lines

# End of line:
grep ")$" app.py               # ")" pe khatam hone wali lines

# One or more:
grep "er+" file.txt            # "er", "err", "errr"...

# Zero or more:
grep "er*" file.txt

# Character class:
grep "[0-9]" file.txt          # Digit wali lines

# Or:
grep "error\|warning" log.txt  # "error" ya "warning"
```

### Extended regex (`-E`):
```bash
grep -E "error|warning|critical" app.log
grep -E "^[0-9]{4}-[0-9]{2}-[0-9]{2}" app.log  # Date format
grep -E "\b\d{3}\b" file.txt    # 3-digit numbers
```

### Real-world Python Developer uses:
```bash
# Project mein Flask import kahan hai?
grep -rn "from flask import" ~/projects

# TODO comments dhundho:
grep -rn "# TODO" . --include="*.py"

# Function definitions:
grep -n "^def " app.py
grep -n "^class " app.py

# Requirements check:
grep "flask\|fastapi\|django" requirements.txt

# Syntax errors dhundho (after running python -m py_compile):
grep "SyntaxError" error_log.txt

# Environment variable usage:
grep -rn "os.environ" . --include="*.py"

# Debug prints dhundho:
grep -rn "print(" . --include="*.py"
```

---

## 📍 `locate` — Fast File Search

`find` se zyada fast hai par real-time nahi (database use karta hai):

```bash
# First time database update karo:
sudo updatedb

# File dhundho:
locate app.py
locate "*.log"

# Case insensitive:
locate -i "README"

# Count results:
locate -c "*.py"
```

**Note:** Naye files turant nahi dikhenge — `sudo updatedb` run karo pehle.

---

## 🔧 `which` aur `whereis` — Commands Dhundho

```bash
# Command kahan installed hai?
which python3
# /usr/bin/python3

which git
# /usr/bin/git

which nginx
# /usr/sbin/nginx

# Aur zyada info:
whereis python3
# python3: /usr/bin/python3 /usr/lib/python3 /usr/lib/python3.10
# (binary, source, man pages)

# Command kya hai type?
type ls
# ls is aliased to 'ls --color=auto'

type cd
# cd is a shell builtin
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: find practice
find . -name "*.md" -type f
find . -name "*.py" -type f
find . -size +1k -type f

# Task 2: grep practice
echo "Hello World" > search_test.txt
echo "Flask is great" >> search_test.txt
echo "Python is awesome" >> search_test.txt
echo "Flask and Python together" >> search_test.txt
echo "ERROR: Something went wrong" >> search_test.txt
echo "WARNING: Low memory" >> search_test.txt
echo "DEBUG: Variable value = 42" >> search_test.txt

# Search karo:
grep "Flask" search_test.txt
grep -i "flask" search_test.txt
grep -n "ERROR\|WARNING" search_test.txt
grep -v "DEBUG" search_test.txt
grep -c "Flask" search_test.txt

# Task 3: Combined search
find . -name "*.txt" -newer README.md
grep -r "flask" . --include="*.txt" 2>/dev/null

# Task 4: System search
which python3 pip3 git
whereis python3
find /usr -name "python3" -type f 2>/dev/null
```

---

## 📖 Summary

```
find . -name "*.py"      = Python files dhundho
find . -type d            = Sirf directories
find . -mtime -1          = Last 24h modified
find . -size +1M          = 1MB se bade
grep "text" file          = Text search
grep -r "text" .          = Recursive search
grep -n "text" file       = With line numbers
grep -i "text" file       = Case insensitive
grep -v "text" file       = Inverted match
locate filename           = Fast search (DB based)
which command             = Command ka path
whereis command           = Command info
```

---

**Next:** `02-Files/03-Text-Viewing.md` → Text efficiently dekhna seekho! 👁️
