# 🐧 Lesson 1: Linux kya hai? — Introduction

---

## 📚 Learning Objectives

Is lesson ke baad tum jaanoge:
- Linux kya hai aur kyun use karte hain
- Windows aur Linux mein kya fark hai
- Different Linux distributions (distros)
- Linux ka use Python developers ke liye kyun important hai
- Terminal kya hota hai

---

## 🤔 Linux kya hai?

**Simple words mein:** Linux ek **Operating System** hai — exactly Windows aur macOS ki tarah. Par ye open-source hai!

```
Windows = Microsoft ki private property (paid)
macOS   = Apple ki private property (Apple computers only)
Linux   = FREE! Koi bhi use kar sakta hai, modify kar sakta hai!
```

### Real-life analogy:
> Linux ek recipe ki tarah hai jo sabke saath share ki gayi hai.
> Windows ek restaurant jaisa hai — tum khana kha sakte ho, par recipe nahi jaante.
> Linux mein tum khana bana bhi sakte ho aur recipe bhi change kar sakte ho!

---

## 🌍 Linux Kahan Use Hota Hai?

```
Android phones     → Linux kernel pe based hai
Web servers        → 96% Linux use karte hain (Google, Facebook, Amazon)
Cloud              → AWS, Azure, GCP — sab Linux
Supercomputers     → 100% Linux!
Smart TVs, routers → Linux embedded
PlayStation        → Linux-based OS
```

**Python Developer ke liye:**
Jab tum Flask ya FastAPI app deploy karoge, wo almost hamesha **Linux server** pe run hogi!

---

## 📊 Linux vs Windows

| Feature | Linux | Windows |
|---------|-------|---------|
| Cost | FREE | Paid |
| Security | Very secure | More vulnerable |
| Speed | Fast, even old PCs | Needs good hardware |
| Programming | Developer friendly | Less optimal |
| Server use | 96% | 4% |
| Terminal | Powerful bash | Limited cmd |

---

## 🐧 Linux Distributions (Distros)

Linux ek **kernel** hai (OS ka core). Uske upar alag companies/communities ne apna OS banaya:

```
Linux Kernel (Core)
        |
        |-- Ubuntu       (Most popular, beginner friendly) <- Hum ye use karenge
        |-- Debian       (Stable, servers mein)
        |-- Fedora       (Cutting-edge features)
        |-- CentOS/RHEL  (Enterprise servers)
        |-- Kali Linux   (Cybersecurity)
        +-- Arch Linux   (Advanced users)
```

GitHub Codespaces **Ubuntu** use karta hai!

```bash
# Check karo kaunsa Linux hai
cat /etc/os-release
```

Expected output:
```
NAME="Ubuntu"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
```

---

## 🖥️ Terminal kya hai?

**Terminal = Linux se baat karne ka tarika** (text-based)

```
Windows way:   Click Start → Search → Click "Notepad"
Linux way:     nano filename.txt   (Enter dabao, ho gaya!)
```

### Terminal ki power:
```bash
# 1000 files ek second mein create karna:
for i in {1..1000}; do touch file_$i.txt; done

# 100 log files mein error dhundna:
grep "ERROR" /var/log/*.log

# Remote server se connect hona:
ssh user@server-ip
```

---

## 🔤 Shell kya hai?

```
Terminal    = Window/App (jo tum dekhte ho)
Shell       = Program jo commands samjhta hai
Bash        = Most common shell (Bourne Again SHell)
```

---

## 💡 Python Developer ke liye Linux kyun Important hai?

### Scenario 1: Server Deployment
```
Tum banate ho:  Flask web app (Windows pe)
Deploy karte ho: Linux VPS server pe
Connect karte ho: SSH se
Manage karte ho: Terminal se
```

### Scenario 2: Docker aur Cloud
```
Docker containers  → Linux pe best run hote hain
AWS EC2            → Ubuntu/Amazon Linux
Azure VM           → Ubuntu/CentOS
GitHub Actions     → Linux environment
```

---

## ❌ Common Beginner Mistakes

### Mistake 1: Case sensitivity bhool jaana
```bash
ls        # SAHI
LS        # ERROR! Linux case-sensitive hai
```

### Mistake 2: Spaces in file names
```bash
touch my file.txt    # GALAT — do alag files ban jaayengi
touch my_file.txt    # SAHI — underscore use karo
```

### Mistake 3: Root se sab karna
```bash
# Hamesha regular user se kaam karo
# sudo sirf zaroori commands pe use karo
```

---

## 🔧 Troubleshooting

### "Command not found" error:
```bash
python   # Error
python3  # SAHI — Codespaces mein python3 use karo

pip      # Error
pip3     # SAHI
```

### Permission denied:
```bash
./script.sh  # Error: Permission denied
chmod +x script.sh
./script.sh  # Ab chalega!
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: System info dekho
uname -a

# Task 2: Current user check karo
whoami

# Task 3: Date/time print karo
date

# Task 4: Linux version check karo
cat /etc/os-release

# Task 5: Disk space dekho
df -h

# Task 6: Fun message print karo
echo "Hello, I am learning Linux!"
```

---

## 📖 Summary

```
Linux      = Free open-source OS
96%        = Web servers Linux use karte hain
Ubuntu     = Best beginner distro (Codespaces mein bhi)
Terminal   = Text-based interface
Shell/Bash = Command processor
Case       = Sensitive! ls aur LS different hain
```

---

**Next Lesson:** `02-Terminal-Basics.md` → Terminal master karo! 🚀
