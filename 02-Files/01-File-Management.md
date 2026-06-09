# 📂 Lesson 1: File Management — Pro Level Operations

---

## 📚 Learning Objectives

- Advanced file operations
- File linking (hard aur soft links)
- File compression aur archiving
- Batch operations
- File metadata samjhna

---

## 🔗 Links — Linux Shortcuts

### Symbolic Links (Soft Links) — Windows Shortcuts ki tarah

```bash
# Syntax:
ln -s original_file link_name

# Example:
ln -s /home/codespace/linux-learning README_LINK.md
ls -la
# lrwxrwxrwx 1 user user 36 Jun 7 README_LINK.md -> /home/codespace/linux-learning/README.md

# Python use case — python3 ko python se access:
which python3
ln -s /usr/bin/python3 /usr/local/bin/python  # (sudo needed)
```

### Hard Links

```bash
# Syntax:
ln original_file hardlink_name

# Same file, alag naam:
ln app.py app_hardlink.py
ls -la app.py app_hardlink.py
# Same inode number!
```

**Difference:**
```
Soft Link: Original file ka path store karta hai (shortcut)
Hard Link: Same data point karta hai (true copy, no size cost)
```

---

## 🗜️ Compression aur Archiving

### `tar` — Files archive karna

```bash
# Create archive (c=create, v=verbose, f=file, z=gzip):
tar -cvzf archive.tar.gz folder/
tar -cvzf backup.tar.gz file1.txt file2.txt folder/

# Extract archive:
tar -xvzf archive.tar.gz

# Specific folder mein extract:
tar -xvzf archive.tar.gz -C /tmp/

# Archive contents dekho (without extracting):
tar -tvzf archive.tar.gz

# bzip2 compression (better but slower):
tar -cvjf archive.tar.bz2 folder/
tar -xvjf archive.tar.bz2
```

**tar flags explain:**
```
c = Create
x = Extract
v = Verbose (show what happening)
z = gzip compression
j = bzip2 compression
f = File name specify karo
t = List contents
```

### `zip` aur `unzip`

```bash
# Zip banao:
zip archive.zip file1.txt file2.txt
zip -r archive.zip folder/    # Recursive (folders ke liye)

# Unzip karo:
unzip archive.zip

# Specific location mein unzip:
unzip archive.zip -d /tmp/extracted/

# Contents dekho:
unzip -l archive.zip
```

### `gzip` — Single file compress karna

```bash
gzip file.txt         # file.txt.gz banta hai (original delete!)
gzip -k file.txt      # Keep original
gunzip file.txt.gz    # Decompress
```

---

## 📊 File Comparison

### `diff` — Do files compare karo

```bash
diff file1.txt file2.txt

# Output meaning:
# < = file1 mein hai, file2 mein nahi
# > = file2 mein hai, file1 mein nahi
# --- aur +++ = unified diff format

# Side by side:
diff -y file1.txt file2.txt

# Directories compare:
diff -r folder1/ folder2/
```

**Python developer ke liye:**
```bash
# Do Python files compare karo:
diff app_v1.py app_v2.py

# requirements.txt versions compare:
diff requirements_old.txt requirements_new.txt
```

---

## 🔄 Batch Operations

### Multiple files handle karna — Wildcards (Globbing)

```bash
# * = Koi bhi characters
ls *.py           # Sab Python files
ls *.txt          # Sab text files
ls file*          # "file" se shuru hone wali sab files

# ? = Ek character
ls file?.txt      # file1.txt, file2.txt (file10.txt nahi!)

# [abc] = Character set
ls file[123].txt  # file1.txt, file2.txt, file3.txt

# {a,b,c} = Alternatives (brace expansion)
touch {jan,feb,mar}_report.txt
# Creates: jan_report.txt  feb_report.txt  mar_report.txt
```

### Bulk rename with `rename` command

```bash
# Install karo (agar nahi hai):
sudo apt-get install rename

# .txt ko .md mein rename:
rename 's/.txt/.md/' *.txt

# Uppercase to lowercase:
rename 'y/A-Z/a-z/' *
```

---

## 📋 File Sorting aur Processing

### `sort` — Sort karo

```bash
sort file.txt              # Alphabetically
sort -n file.txt           # Numerically
sort -r file.txt           # Reverse
sort -u file.txt           # Unique (duplicates remove)

# CSV/TSV files:
sort -t',' -k2 data.csv    # 2nd column se sort
```

### `uniq` — Duplicate lines

```bash
sort file.txt | uniq       # Unique lines
sort file.txt | uniq -c    # Count occurrences
sort file.txt | uniq -d    # Sirf duplicates
```

### `cut` — Columns nikalo

```bash
# CSV se specific column:
cut -d',' -f2 data.csv     # Comma-separated, column 2

# Tab-separated, columns 1 aur 3:
cut -f1,3 data.tsv

# Character position:
cut -c1-10 file.txt        # First 10 characters
```

### `awk` — Powerful text processing

```bash
# Column 2 print karo:
awk '{print $2}' file.txt

# Filter aur print:
awk '/ERROR/ {print $0}' app.log

# Sum column:
awk '{sum += $3} END {print sum}' data.txt

# CSV process:
awk -F',' '{print $1, $3}' data.csv
```

### `sed` — Stream editor (find and replace)

```bash
# Replace text:
sed 's/old_text/new_text/' file.txt        # First occurrence
sed 's/old_text/new_text/g' file.txt       # All occurrences
sed -i 's/old_text/new_text/g' file.txt    # In-place (original modify!)

# Lines delete:
sed '5d' file.txt             # Line 5 delete
sed '/pattern/d' file.txt     # Pattern wali lines delete

# Lines add:
sed '3a\New line here' file.txt  # Line 3 ke baad add
```

**Python developer ke liye:**
```bash
# requirements.txt mein version update:
sed -i 's/flask==2.2.0/flask==2.3.0/g' requirements.txt

# Debug statements remove karo:
sed -i '/print("DEBUG/d' app.py
```

---

## 📌 File Metadata Commands

### `stat` — File details

```bash
stat file.txt
```
```
File: file.txt
Size: 1024            Blocks: 8          IO Block: 4096   regular file
Device: sda1          Inode: 1234567     Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/codespace)   Gid: (1000/codespace)
Access:  2026-06-07 10:00:00
Modify:  2026-06-07 10:00:00
Change:  2026-06-07 10:00:00
```

### `file` — File type batao

```bash
file image.jpg
# image.jpg: JPEG image data

file script.sh
# script.sh: Bourne-Again shell script

file data.json
# data.json: ASCII text

file compressed.tar.gz
# compressed.tar.gz: gzip compressed data
```

---

## 🏋️ Practice Tasks

```bash
# Task 1: Project backup banana
mkdir ~/backups
tar -cvzf ~/backups/flask_blog_backup.tar.gz flask_blog/
ls -lh ~/backups/

# Task 2: Wildcards practice
find flask_blog -name "*.html"
ls flask_blog/**/*.html 2>/dev/null || find flask_blog -name "*.html"

# Task 3: File comparison
echo "Version 1" > v1.txt
echo "Version 1" >> v1.txt
echo "Common line" >> v1.txt

echo "Version 2" > v2.txt
echo "Common line" >> v2.txt
echo "New in V2" >> v2.txt

diff v1.txt v2.txt

# Task 4: Text processing
echo -e "banana\napple\ncherry\napple\nbanana\norange" > fruits.txt
sort fruits.txt
sort fruits.txt | uniq
sort fruits.txt | uniq -c | sort -rn

# Task 5: sed practice
cp flask_blog/requirements.txt requirements_backup.txt
sed 's/flask==/FLASK==/g' flask_blog/requirements.txt
cat flask_blog/requirements.txt  # Original unchanged! (no -i flag)
```

---

## 📖 Summary

```
ln -s       = Symbolic link (shortcut)
tar -cvzf   = Archive + compress karo
tar -xvzf   = Extract archive
zip/unzip   = Zip operations
diff        = Files compare karo
sort        = Sort karo
uniq        = Duplicate lines handle
cut         = Columns extract
awk         = Text processing powerhouse
sed         = Find and replace
stat        = File metadata
file        = File type detect
```

---

**Next:** `02-Files/02-Searching-Files.md` → Files dhundna master karo! 🔍
