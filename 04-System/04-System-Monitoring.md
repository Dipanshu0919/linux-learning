# 📊 Lesson 4: System Monitoring — Health Check Karo!

---

## 📚 Learning Objectives

- CPU, Memory, Disk monitoring
- Network usage dekhna
- System performance samjhna
- Alerts setup karna

---

## 💻 CPU Monitoring

```bash
# CPU info:
cat /proc/cpuinfo | grep "model name\|processor" | head -10
nproc              # Number of CPUs/cores

# CPU usage:
top -bn1 | head -5
mpstat 1 5         # Per-second CPU stats (apt install sysstat)

# Load average:
uptime
# 10:30:00 up 2 days, 3:45, 1 user, load average: 0.12, 0.08, 0.05
#                                                    |      |      |
#                                                   1min   5min  15min
# Rule: Load average should be < number of CPU cores
```

---

## 🧠 Memory Monitoring

```bash
# RAM status:
free -h
# Or:
cat /proc/meminfo | head -10

# Memory usage details:
vmstat 1 5        # Virtual memory stats
vmstat -s         # Summary

# Swap:
swapon --show
cat /proc/swaps
```

---

## 💾 Disk Monitoring

```bash
# Disk usage:
df -h             # All filesystems
df -h /           # Root only
df -Th            # Filesystem type ke saath

# Folder sizes:
du -sh /var/log
du -sh /home/*
du -sh * | sort -rh | head -10    # Biggest folders

# Disk I/O:
iostat 1 5        # (apt install sysstat)
iotop             # Real-time (sudo apt install iotop)
```

---

## 🌐 Network Monitoring

```bash
# Network interfaces:
ip addr show
ifconfig          # (apt install net-tools)

# Network stats:
netstat -i        # Interface stats
ss -tulpn         # Open ports aur connections

# Bandwidth usage:
nethogs           # Per-process bandwidth (sudo apt install nethogs)
nload             # Real-time bandwidth (sudo apt install nload)
iftop             # Per-connection bandwidth

# Active connections:
ss -tn            # TCP connections
ss -un            # UDP connections
netstat -an | grep ESTABLISHED | wc -l  # Active connection count
```

---

## 🔧 Useful Monitoring Tools

```bash
# Install useful tools:
sudo apt-get install -y htop iotop nethogs nload sysstat

# htop - Interactive process viewer:
htop

# dstat - All-in-one stats:
sudo apt-get install dstat
dstat             # CPU, disk, network ek saath

# glances - System overview:
sudo pip3 install glances
glances
```

---

## 📝 Simple Monitoring Script

```bash
cat > system_check.sh << 'EOF'
#!/bin/bash

echo "====== SYSTEM HEALTH CHECK ======"
echo "Date: $(date)"
echo ""

# CPU Load
echo "--- CPU Load Average ---"
uptime | awk -F'load average:' '{print "Load:" $2}'

# Memory
echo ""
echo "--- Memory Usage ---"
free -h | awk '/Mem/ {printf "Used: %s / Total: %s (%.1f%%)\n", $3, $2, $3/$2*100}'

# Disk
echo ""
echo "--- Disk Usage ---"
df -h / | awk 'NR==2 {print "Root disk: " $3 " used of " $2 " (" $5 " used)"}'

# Top 5 processes by CPU
echo ""
echo "--- Top 5 CPU Processes ---"
ps aux --sort=-%cpu | head -6 | awk '{print $1, $2, $3"%", $11}' | column -t

# Top 5 by Memory
echo ""
echo "--- Top 5 Memory Processes ---"
ps aux --sort=-%mem | head -6 | awk '{print $1, $2, $4"%", $11}' | column -t

echo ""
echo "====== END OF REPORT ======"
EOF

chmod +x system_check.sh
./system_check.sh
```

---

## 🏋️ Practice

```bash
# Comprehensive system check:
echo "=== CPU ===" && nproc && uptime
echo "=== MEMORY ===" && free -h
echo "=== DISK ===" && df -h /
echo "=== TOP PROCESSES ===" && ps aux --sort=-%cpu | head -6
echo "=== NETWORK ===" && ss -tulpn | head -10
```

---

## 📖 Summary

```
uptime         = Load average
free -h        = Memory usage
df -h          = Disk usage
du -sh dir     = Directory size
ps aux         = All processes
top / htop     = Interactive monitor
ss -tulpn      = Open ports
iostat         = Disk I/O
```

---

**Next:** `04-System/Exercises.md` → Practice karo! 💪
