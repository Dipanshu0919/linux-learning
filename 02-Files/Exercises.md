# 🏋️ 02-Files: Complete Exercises

---

## Exercise 1: File Searching Master

```bash
# Setup karo pehle:
mkdir search_practice
cd search_practice

# Different file types banao:
touch app.py model.py views.py utils.py __init__.py
touch index.html about.html contact.html
touch style.css main.css
touch readme.txt notes.txt todo.txt
touch config.json data.json
touch app.log error.log debug.log

# Exercise 1a: Sab Python files dhundho
find . -name "*.py"

# Exercise 1b: Sab HTML files
find . -name "*.html"

# Exercise 1c: Sab log files
find . -name "*.log"

# Exercise 1d: Sab files alphabetically sorted
find . -type f | sort

# Exercise 1e: File count by type
find . -name "*.py" | wc -l
find . -name "*.html" | wc -l
find . -name "*.log" | wc -l

cd ..
```

---

## Exercise 2: Content Writing & Searching

```bash
# Log file banao:
cat > sample.log << 'EOF'
2026-01-01 10:00:00 INFO Application started
2026-01-01 10:01:00 DEBUG Database connected
2026-01-01 10:02:00 WARNING Low memory: 85% used
2026-01-01 10:03:00 ERROR Failed to connect to API
2026-01-01 10:04:00 INFO Request received from 192.168.1.1
2026-01-01 10:05:00 ERROR Timeout after 30 seconds
2026-01-01 10:06:00 DEBUG Query executed in 0.5s
2026-01-01 10:07:00 INFO Response sent successfully
2026-01-01 10:08:00 CRITICAL Database connection lost!
2026-01-01 10:09:00 ERROR Cannot reach database server
EOF

# Exercise 2a: Sirf ERROR lines
grep "ERROR" sample.log

# Exercise 2b: ERROR ya WARNING
grep "ERROR\|WARNING" sample.log

# Exercise 2c: Line numbers ke saath
grep -n "ERROR" sample.log

# Exercise 2d: Count karo
grep -c "ERROR" sample.log
grep -c "WARNING" sample.log

# Exercise 2e: INFO nahi chahiye
grep -v "INFO" sample.log

# Exercise 2f: Specific IP
grep "192.168" sample.log

# Exercise 2g: CRITICAL lines + context
grep -A 1 -B 1 "CRITICAL" sample.log
```

---

## Exercise 3: Vim Practice (Minimum Viable Vim)

```bash
# Ye commands ek ke baad ek karo:

# Step 1: File banao vim se
vim my_script.py

# Vim mein (type these exactly):
# i (insert mode mein jao)
# Then type:
# #!/usr/bin/env python3
# 
# def main():
#     print("Hello from Vim!")
#     print("I learned Linux editing!")
# 
# if __name__ == "__main__":
#     main()
#
# Esc dabao
# :wq dabao
# Enter dabao

# Step 2: File run karo
python3 my_script.py
```

---

## 🔥 Mini Project: Log Analyzer Script

```bash
# log_analyzer.sh banao:
cat > log_analyzer.sh << 'EOF'
#!/bin/bash

LOG_FILE="sample.log"

echo "=== LOG ANALYSIS REPORT ==="
echo "File: $LOG_FILE"
echo "Total Lines: $(wc -l < $LOG_FILE)"
echo ""
echo "--- By Level ---"
echo "ERROR:    $(grep -c 'ERROR' $LOG_FILE)"
echo "WARNING:  $(grep -c 'WARNING' $LOG_FILE)"
echo "INFO:     $(grep -c 'INFO' $LOG_FILE)"
echo "DEBUG:    $(grep -c 'DEBUG' $LOG_FILE)"
echo "CRITICAL: $(grep -c 'CRITICAL' $LOG_FILE)"
echo ""
echo "--- Critical Issues ---"
grep "ERROR\|CRITICAL" $LOG_FILE
EOF

chmod +x log_analyzer.sh
./log_analyzer.sh
```

---

## ✅ Self-Check

- [ ] find se files dhundh sakta/sakti hoon by name, type, size, time
- [ ] grep se text search kar sakta/sakti hoon (basic aur advanced)
- [ ] nano mein file create, edit, save kar sakta/sakti hoon
- [ ] vim mein enter ho sakta/sakti hoon, type kar sakta/sakti hoon, save karke bahar aa sakta/sakti hoon
- [ ] tail -f se live file monitor kar sakta/sakti hoon

**Sab complete? Next chapter pe jao!** 🎉
