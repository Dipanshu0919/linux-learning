# 🏋️ 04-System: Exercises

---

## Exercise 1: Process Management

```bash
# 1. Background job banao:
sleep 200 &
sleep 300 &
sleep 400 &

# 2. Jobs dekho:
jobs

# 3. Process list:
ps aux | grep sleep

# 4. Job 2 ko foreground mein laao:
fg 2
# Ctrl+Z (pause karo)
bg 2  # Background mein bhejo

# 5. Sab sleep processes kill karo:
pkill sleep
jobs  # Empty hona chahiye
```

---

## Exercise 2: Service Management

```bash
# 1. Active services list:
systemctl list-units --type=service --state=active | head -15

# 2. Failed services:
systemctl list-units --type=service --state=failed

# 3. Any service status:
sudo systemctl status cron
```

---

## Exercise 3: Log Analysis

```bash
# Fake log banao:
cat > /tmp/test_app.log << 'EOF'
2026-06-07 10:00:01 INFO Server started
2026-06-07 10:00:02 DEBUG Database connected
2026-06-07 10:01:00 INFO Request: GET / from 192.168.1.1
2026-06-07 10:01:01 INFO Response: 200 OK
2026-06-07 10:02:00 WARNING Memory at 80%
2026-06-07 10:03:00 ERROR Database timeout
2026-06-07 10:03:01 ERROR Retry attempt 1
2026-06-07 10:03:05 ERROR Retry attempt 2
2026-06-07 10:03:10 INFO Database reconnected
2026-06-07 10:05:00 CRITICAL Disk space critical: 95%
EOF

# Analyze:
grep "ERROR" /tmp/test_app.log
grep "WARNING\|ERROR\|CRITICAL" /tmp/test_app.log
grep -c "ERROR" /tmp/test_app.log
grep "10:03" /tmp/test_app.log
```

---

## 🔥 Mini Project: System Monitor Script

```bash
cat > ~/monitor.sh << 'EOF'
#!/bin/bash
# Continuous system monitor

while true; do
    clear
    echo "=== Live System Monitor (Ctrl+C to stop) ==="
    echo "Time: $(date '+%H:%M:%S')"
    echo ""
    echo "CPU Load: $(uptime | awk -F'load average:' '{print $2}')"
    echo "Memory: $(free -h | awk '/Mem/ {print $3 "/" $2}')"
    echo "Disk /: $(df -h / | awk 'NR==2 {print $3 "/" $2 " (" $5 ")"}')"
    echo ""
    echo "Top 3 Processes:"
    ps aux --sort=-%cpu | awk 'NR>1 && NR<=4 {printf "  %-15s CPU:%-5s MEM:%-5s\n", $11, $3, $4}'
    sleep 2
done
EOF

chmod +x ~/monitor.sh
# ~/monitor.sh   # Uncomment to run (Ctrl+C to stop)
```

---

## ✅ Self-Check

- [ ] ps aux se processes dekh sakta/sakti hoon
- [ ] kill aur pkill se processes band kar sakta/sakti hoon
- [ ] Background jobs manage kar sakta/sakti hoon (&, fg, bg, jobs)
- [ ] systemctl se service start/stop/status kar sakta/sakti hoon
- [ ] journalctl se logs dekh sakta/sakti hoon
- [ ] free -h aur df -h se system resources check kar sakta/sakti hoon

**Next Module:** `05-Networking` → Network aur SSH seekho! 🌐
