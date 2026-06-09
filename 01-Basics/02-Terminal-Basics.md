# 🖥️ Lesson 2: Terminal Basics — Apna Command Center

---

## 📚 Learning Objectives

- Terminal aur Shell ka difference samjhna
- Basic terminal navigation
- Command structure samjhna
- Help kaise lein (man pages)
- Shortcuts jo productivity 10x badha denge

---

## 🖥️ Terminal kya hai? (Deep Dive)

Terminal ek **window** hai jo tumhe Shell se interact karne deta hai.

```
+------------------------------------------+
|  Terminal Window                          |
|                                           |
|  user@hostname:~$  _                     |
|                 ^                         |
|                 |                         |
|            Ye "prompt" hai               |
+------------------------------------------+
```

### Prompt ka matlab:
```
user@hostname:~$

user     = Tumhara username (login name)
@        = at (matlab: user "at" hostname)
hostname = Computer/server ka naam
~        = Current directory (~ matlab HOME folder)
$        = Regular user (# matlab root/admin user)
```

---

## 📝 Command Structure

Har Linux command is format mein hoti hai:

```
command [OPTIONS] [ARGUMENTS]
   |         |         |
   |         |         +-- Kisi pe action karo (file, folder, etc.)
   |         +-- Command ka behaviour change karo
   +-- Jo karna hai wo action
```

### Examples:
```bash
ls                  # Command only — current folder list karo
ls -l               # Command + Option — detailed list
ls -l /home         # Command + Option + Argument — /home ki detailed list
ls -la /home        # Multiple options ek saath
```

---

## 🔤 Essential Terminal Commands (First Day)

### 1. `echo` — Text print karna

```bash
# Syntax:
echo "message"

# Examples:
echo "Hello World!"
echo "Mera naam Python hai"
echo "2 + 2 = $((2 + 2))"  # Math bhi kar sakta hai!
```

**Expected output:**
```
Hello World!
Mera naam Python hai
2 + 2 = 4
```

**Why use it?** Scripts mein messages print karna, debugging, variable values check karna.

---

### 2. `pwd` — Print Working Directory (Tum kahan ho?)

```bash
# Syntax:
pwd

# Example:
pwd
```

**Expected output:**
```
/home/codespace
```

**Breakdown:**
```
/ = Root directory (sab kuch yahan se start hota hai)
home = Users ke folders
codespace = Tumhara username
```

**Real-life analogy:** GPS pe "Current Location" button jaisa!

---

### 3. `ls` — List (Files aur folders dekhna)

```bash
# Basic list
ls

# Detailed list (-l flag)
ls -l

# Hidden files bhi dikhaao (-a flag)
ls -a

# Both combined
ls -la

# Specific folder ki list
ls /home

# Human readable sizes
ls -lh
```

**Expected outputs:**
```bash
$ ls
01-Basics  02-Files  README.md  START_HERE.md

$ ls -l
total 48
drwxr-xr-x 2 user user 4096 Jun  7 10:00 01-Basics
drwxr-xr-x 2 user user 4096 Jun  7 10:00 02-Files
-rw-r--r-- 1 user user 2048 Jun  7 10:00 README.md

$ ls -la
total 52
drwxr-xr-x 5 user user 4096 Jun  7 10:00 .
drwxr-xr-x 3 user user 4096 Jun  7 09:00 ..
-rw-r--r-- 1 user user  220 Jun  7 09:00 .bash_profile
drwxr-xr-x 2 user user 4096 Jun  7 10:00 01-Basics
```

**`ls -l` output breakdown:**
```
drwxr-xr-x  2  user  user  4096  Jun 7 10:00  01-Basics
|           |   |     |     |    |             |
|           |   |     |     |    |             +-- Name
|           |   |     |     |    +-- Last modified date
|           |   |     |     +-- Size in bytes
|           |   |     +-- Group owner
|           |   +-- Owner
|           +-- Number of links
+-- Permissions (d=directory, -=file, r=read, w=write, x=execute)
```

---

### 4. `cd` — Change Directory (Folder badalna)

```bash
# Kisi folder mein jao
cd 01-Basics

# Ek level upar jao (parent folder)
cd ..

# Do level upar jao
cd ../..

# Home folder mein jao (kahan bhi ho)
cd ~
cd        # Shortcut (~ same)

# Root directory mein jao
cd /

# Full path se jao
cd /home/codespace/linux-learning
```

**Practice sequence:**
```bash
pwd              # /home/codespace/linux-learning
cd 01-Basics     # 01-Basics folder mein gaye
pwd              # /home/codespace/linux-learning/01-Basics
cd ..            # Wapas aaye
pwd              # /home/codespace/linux-learning
cd ~             # Home folder
pwd              # /home/codespace
```

---

### 5. `clear` — Terminal saaf karna

```bash
clear
# Ya shortcut: Ctrl + L
```

---

### 6. `history` — Commands ka history

```bash
# Pichli commands dekho
history

# Last 10 commands
history 10

# Specific command search karo
history | grep "ls"
```

---

## ⌨️ Super Useful Terminal Shortcuts

| Shortcut | Kya karta hai | Kab use karein |
|----------|---------------|----------------|
| `Tab` | Auto-complete | Command/file name type karte waqt |
| `Tab Tab` | All possibilities dikhaao | Options dekhne ke liye |
| `↑ Arrow` | Previous command | Command dobara use karna |
| `↓ Arrow` | Next command | Commands mein navigate karna |
| `Ctrl + C` | Running command band karo | Program stuck hone pe |
| `Ctrl + L` | Clear terminal | Clean screen |
| `Ctrl + A` | Cursor line ke start pe | Long command edit karna |
| `Ctrl + E` | Cursor line ke end pe | Long command edit karna |
| `Ctrl + R` | History search | Purani command dhundna |
| `!!` | Last command dobara chalao | Sudo lgaana bhool gaye? |
| `!ls` | Last 'ls' command chalao | Specific command repeat |

### Tab Completion — Most Useful Shortcut!

```bash
# "01" type karo, phir Tab dabao
cd 01<Tab>
# Auto-complete ho jaayega: cd 01-Basics/

# Partial file name
cat RE<Tab>
# Ho jaayega: cat README.md
```

---

## 📖 Help Kaise Lein?

### 1. `man` — Manual Pages

```bash
# Command ka manual dekho
man ls
man cd
man echo

# Manual mein navigate karna:
# j ya Down Arrow = scroll down
# k ya Up Arrow   = scroll up
# /word           = search
# q               = quit (bahar aao)
```

### 2. `--help` flag

```bash
# Quick help
ls --help
cd --help
```

### 3. `whatis` — One-line description

```bash
whatis ls
# Output: ls (1)      - list directory contents

whatis pwd
# Output: pwd (1)     - print name of current/working directory
```

### 4. `type` — Command kahan hai?

```bash
type ls
# Output: ls is /usr/bin/ls

type cd
# Output: cd is a shell builtin
```

---

## 🔧 Troubleshooting

### "bash: cd: too many arguments"
```bash
cd My Folder    # GALAT — space hai!
cd "My Folder"  # SAHI — quotes use karo
cd My\ Folder   # SAHI — backslash use karo
```

### Terminal hang ho gaya
```bash
Ctrl + C   # Current process ko kill karo
Ctrl + Z   # Background mein bhejo
Ctrl + D   # Terminal close karo (ya "exit" type karo)
```

### Command galti se gaya
```bash
# Ek step wapas nahi jaata Linux mein! Isliye socho pehle.
# Par practice environment mein sab theek hai.
```

---

## 🏋️ Exercises

**Exercise 1: Navigation Practice**
```bash
# Ye sequence follow karo:
pwd
cd /home
pwd
cd ~
pwd
cd /tmp
pwd
cd -     # Previous directory mein wapas jao!
pwd
```

**Exercise 2: ls ka Master Bano**
```bash
ls
ls -l
ls -la
ls -lh
ls /tmp
ls /etc | head -20   # /etc ki first 20 entries
```

**Exercise 3: Tab Completion Practice**
```bash
# Type karo aur Tab dabao:
cd 01<Tab>
cat RE<Tab>
ls /ho<Tab>
```

**Exercise 4: History Use Karo**
```bash
history
history | grep "cd"
!pwd    # Last pwd command dobara chalao
```

---

## 📖 Summary

```
pwd       = Current location batata hai
ls        = Files/folders list karta hai
cd        = Directory change karta hai
echo      = Text print karta hai
clear     = Terminal saaf karta hai
history   = Purani commands dekhta hai
man CMD   = Command ka manual dikhaata hai
Tab       = Auto-complete (MOST USEFUL!)
Ctrl+C    = Program band karo
```

---

**Next Lesson:** `03-Filesystem.md` → Linux ka file system samjho! 📁
