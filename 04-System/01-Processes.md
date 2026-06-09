# ⚙️ Lesson 1: Processes — Running Programs ko Control Karo

---

## 📚 Learning Objectives

- Process kya hota hai samjhna
- Running processes dekhna
- Processes kill/stop karna
- Background aur foreground jobs
- System resource usage dekhna

---

## 🔄 Process kya hai?

**Process = Chalta hua program**

Jab tum `python3 app.py` run karte ho, ek process create hoti hai.

```
Program (app.py) → Run karo → Process (PID: 1234)
                                 |
                                 |-- CPU use karta hai
                                 |-- Memory use karta hai
                                 |-- Files use kar sakta hai
```

### Process ke Types:
```
Foreground Process = Terminal mein dikhai deta hai (tum directly interact karte ho)
Background Process = Peeche chalta hai (&) 
Daemon Process     = System service (nginx, sshd, etc.) - background mein hamesha
Zombie Process     = Process jo khatam hua par entry nahi gayi
Orphan Process     = Parent process khatam ho gaya
```

---

## 🔍 Processes Dekhna

### `ps` — Process Status

```bash
# Current terminal ke processes:
ps

# Sab processes (full info):
ps aux

# Specific process dhundho:
ps aux | grep python3
ps aux | grep nginx

# Tree format:
ps axjf
# Ya:
pstree
```

### `ps aux` output samjhna:
```
USER    PID  %CPU %MEM    VSZ   RSS TTY   STAT  START   TIME COMMAND
root      1   0.0  0.1  33620  6756 ?     Ss    Jun07   0:01 /sbin/init
www-data  892  0.1  0.5 216284 23456 ?    S     10:00   0:02 nginx: worker
codespace 1234 2.5  1.2 456789 51234 pts/0 S   10:05   0:10 python3 app.py

# PID    = Process ID (unique number)
# %CPU   = CPU usage
# %MEM   = Memory usage
# VSZ    = Virtual memory size (KB)
# RSS    = Physical memory used (KB)
# STAT   = Process state (S=sleeping, R=running, Z=zombie, T=stopped)
# TIME   = Total CPU time
```

---

## 📊 `top` — Live Process Monitor

```bash
top
```

```
top - 10:30:00 up 2 days, 3:45,  1 user,  load average: 0.12, 0.08, 0.05
Tasks:  95 total,   1 running,  94 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.5 us,  0.8 sy,  0.0 ni, 96.5 id,  0.2 wa
MiB Mem :  15851.9 total,   8362.1 free,   4123.7 used,   3366.1 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.  11250.9 avail Mem

  PID USER    PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 user    20   0  456789  51234  12345 R   2.5   0.3   0:10.23 python3
  892 www-data 20  0  216284  23456   8765 S   0.1   0.1   0:02.45 nginx
```

### `top` ke shortcuts:
```
q     = Quit
k     = Kill process (PID puchega)
P     = Sort by CPU usage (default)
M     = Sort by Memory usage
N     = Sort by PID
1     = Show individual CPU cores
h     = Help
u     = Filter by user
```

---

## 🔥 `htop` — Better top (Install karna padega)

```bash
# Install:
sudo apt-get install htop

# Run:
htop
```

`htop` zyada colorful aur user-friendly hai!

---

## ⚡ `kill` — Process Band Karna

```bash
# Syntax:
kill [signal] PID

# Gentle kill (process ko cleanup karne ka mauka):
kill PID
kill -15 PID    # SIGTERM (same as above)

# Force kill (turant band):
kill -9 PID     # SIGKILL (process ko seedha band!)

# Process naam se kill:
pkill python3
pkill -9 nginx

# Pattern se kill:
pkill -f "python3 app.py"    # Full command match

# All matching processes:
killall nginx
killall -9 python3
```

### Common Signals:
```
Signal  Number  Kaam
SIGTERM   15    Graceful shutdown (default)
SIGKILL    9    Force kill (cleanup nahi hogi!)
SIGHUP     1    Reload config (restart without stopping)
SIGSTOP   19    Process pause karo
SIGCONT   18    Paused process continue karo
```

---

## 🔄 Background aur Foreground Jobs

```bash
# Program foreground mein run karo (normal):
python3 app.py
# Ctrl+C se band hoga

# Program background mein run karo:
python3 app.py &
# Output: [1] 1234  (job number aur PID)

# Background jobs dekho:
jobs
# Output: [1]+ Running   python3 app.py &

# Background job foreground mein laao:
fg 1        # Job number 1 ko foreground mein
fg %1       # Same thing

# Foreground job ko background mein bhejo:
# Ctrl+Z dabao (process pause hota hai)
bg 1        # Resume in background

# Background job kill karo:
kill %1     # Job number se
```

### Nohup — Terminal band hone ke baad bhi run karo:
```bash
nohup python3 app.py &
# Output: nohup: ignoring input and appending output to 'nohup.out'

# Log file dekho:
tail -f nohup.out
```

---

## 🔢 Process Priority — nice aur renice

```bash
# Lower priority se run karo (nice value 0-19, higher = lower priority):
nice -n 10 python3 heavy_script.py

# Running process ki priority change karo:
renice -n 5 -p 1234    # PID 1234 ki priority change

# CPU intensive task ko low priority se run karo:
nice -n 19 tar -cvzf backup.tar.gz large_folder/
```

---

## 📊 `pgrep` — Process PID Dhundho

```bash
# Process naam se PID:
pgrep python3
pgrep nginx
pgrep -l python3    # Naam ke saath

# Full command match:
pgrep -f "python3 app.py"
```

---

## 🏋️ Practice

```bash
# Task 1: Apne processes dekho
ps aux | grep $(whoami)

# Task 2: System load dekho
uptime
top -bn1 | head -20

# Task 3: Background job practice
sleep 100 &       # 100 second sleep background mein
jobs              # Dekho
ps aux | grep sleep
kill %1           # Kill karo

# Task 4: Process tree
pstree | head -20
# Ya:
ps axjf | head -30

# Task 5: Python process test
python3 -c "import time; time.sleep(60)" &
pgrep python3
kill $(pgrep python3)  # Kill karo
```

---

## 📖 Summary

```
ps aux          = Sab processes dekho
top             = Live process monitor
htop            = Better version (install karo)
kill PID        = Graceful kill
kill -9 PID     = Force kill
pkill name      = Naam se kill
pgrep name      = PID dhundho
cmd &           = Background mein chalao
jobs            = Background jobs dekho
fg N            = Foreground mein laao
nohup cmd &     = Terminal close hone ke baad bhi chalao
```

---

**Next:** `04-System/02-System-Services.md` → systemctl master karo! 🛠️
