# 👁️ Lesson 3: Text Viewing — Files Efficiently Dekhna

---

## 📚 Learning Objectives

- Large files efficiently dekhna
- Live log monitoring
- Multiple files simultaneously dekhna
- Text mein navigate karna

---

## 📖 `cat` — Complete File Print

```bash
cat file.txt              # Print karo
cat -n file.txt           # Line numbers ke saath
cat -A file.txt           # Special characters dikhaao
cat file1.txt file2.txt   # Multiple files combine

# Create file:
cat > newfile.txt << EOF
Line 1
Line 2
EOF
```

---

## 📜 `less` — Best Way to Read Large Files

```bash
less /var/log/syslog
less requirements.txt
```

**Navigation shortcuts:**
```
Space / f / PgDn  = Next page
b / PgUp          = Previous page  
j / Down          = One line down
k / Up            = One line up
G                 = End of file
g                 = Beginning of file
/search_text      = Search (n=next, N=previous)
?search_text      = Search backward
:n                = Next file
:p                = Previous file
q                 = Quit
h                 = Help
```

---

## ⬆️ `head` — First Lines Dekhna

```bash
head file.txt              # First 10 lines (default)
head -5 file.txt           # First 5 lines
head -20 file.txt          # First 20 lines
head -n 15 file.txt        # First 15 lines
```

---

## ⬇️ `tail` — Last Lines Dekhna + Live Monitoring

```bash
tail file.txt              # Last 10 lines
tail -20 file.txt          # Last 20 lines
tail -f app.log            # LIVE monitoring! (Follow mode)
tail -F app.log            # Follow + file rotation handle
tail -n +5 file.txt        # Line 5 se end tak
```

### `tail -f` — DevOps ka superhero!

```bash
# Flask app ke logs live dekhna:
tail -f /var/log/nginx/access.log &   # Background mein
# App use karo, logs real-time dikhenge!

# Multiple files simultaneously:
tail -f app.log error.log
```

---

## 📊 `more` — Page by Page (Old style)

```bash
more file.txt     # Space=next page, q=quit
```

`less` use karo — ye zyada powerful hai!

---

## 🔢 Lines aur Words Count

```bash
wc file.txt                # Lines words bytes
wc -l file.txt             # Sirf lines
wc -w file.txt             # Sirf words
wc -c file.txt             # Sirf bytes

# Multiple files:
wc -l *.py                 # Har Python file ki lines
wc -l *.py | sort -n       # Sort by lines
```

---

## 🎨 Syntax Highlighting

```bash
# bat install karo (better cat):
sudo apt install bat 2>/dev/null || sudo apt install batcat 2>/dev/null

# Use:
bat app.py        # Syntax highlighting ke saath!
batcat app.py     # (Ubuntu pe batcat)
```

---

## 🏋️ Practice

```bash
# Setup:
for i in {1..100}; do echo "Line $i: Sample data here"; done > large_file.txt

# Practice:
head -5 large_file.txt
tail -5 large_file.txt
wc -l large_file.txt
less large_file.txt  # q se bahar aao
grep "Line 5" large_file.txt
```

---

## 📖 Summary

```
cat       = Complete file print
less      = Page-by-page (interactive)
head -N   = First N lines
tail -N   = Last N lines
tail -f   = Live monitoring (MOST USEFUL!)
wc -l     = Line count
more      = Old page-by-page viewer
```

---

**Next:** `02-Files/04-Editors.md` → Vim aur Nano seekho! ✏️
