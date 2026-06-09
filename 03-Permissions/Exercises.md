# 🏋️ 03-Permissions: Exercises

---

## Exercise 1: Permission Reading

```bash
# Ye output read karo aur samjho:
ls -la /home/
# Total kya matlab?
# d vs - kya matlab?
# rwx ka matlab kya?
# Owner aur group kya hai?
```

---

## Exercise 2: chmod Practice

```bash
mkdir perms_practice && cd perms_practice
touch file1.txt file2.txt script.sh

# Task 1: file1.txt ko owner-only private banao (600)
chmod 600 file1.txt
ls -la file1.txt

# Task 2: script.sh ko executable banao (755)
chmod 755 script.sh
ls -la script.sh

# Task 3: file2.txt ko read-only for everyone (444)
chmod 444 file2.txt
ls -la file2.txt

# Test: file2 mein likhne ki koshish:
echo "test" >> file2.txt   # Permission denied hona chahiye!

# Restore:
chmod 644 file2.txt

# Task 4: Symbolic method se permission add/remove
chmod u+x file1.txt    # Execute add
chmod g-w file1.txt    # Group write remove
chmod o=r file1.txt    # Others sirf read
ls -la file1.txt

cd ..
```

---

## Exercise 3: Security Setup for Python Project

```bash
# Real-world scenario: Flask project secure karo

mkdir secure_flask_project && cd secure_flask_project
touch app.py .env requirements.txt run.sh
mkdir -p templates static

# .env file mein secrets:
echo "SECRET_KEY=my_super_secret_key_123" > .env
echo "DATABASE_URL=postgresql://localhost/mydb" >> .env
echo "API_KEY=abc123xyz" >> .env

# Secure permissions set karo:
chmod 600 .env             # Only owner can read!
chmod 644 app.py           # Standard file
chmod 644 requirements.txt # Standard file
chmod 755 run.sh           # Executable
chmod 755 templates/       # Directory
chmod 755 static/          # Directory

# Verify:
ls -la

echo "Security check:"
echo ".env permissions:"
stat -c "%a %n" .env      # Should be 600!

cd ..
```

---

## 🔥 Mini Challenge

```bash
# Challenge: Ek script banao jo project permissions fix kare

cat > fix_permissions.sh << 'EOF'
#!/bin/bash

PROJECT_DIR=${1:-.}  # Argument ya current directory

echo "Fixing permissions for: $PROJECT_DIR"

# Directories: 755
find $PROJECT_DIR -type d -exec chmod 755 {} \;

# Python files: 644
find $PROJECT_DIR -name "*.py" -exec chmod 644 {} \;

# Shell scripts: 755 (executable)
find $PROJECT_DIR -name "*.sh" -exec chmod 755 {} \;

# .env files: 600 (private!)
find $PROJECT_DIR -name ".env" -exec chmod 600 {} \;

# HTML/CSS/JS files: 644
find $PROJECT_DIR -name "*.html" -exec chmod 644 {} \;
find $PROJECT_DIR -name "*.css" -exec chmod 644 {} \;
find $PROJECT_DIR -name "*.js" -exec chmod 644 {} \;

echo "Done! Permissions fixed."
ls -la $PROJECT_DIR
EOF

chmod +x fix_permissions.sh
./fix_permissions.sh secure_flask_project/
```

---

## ✅ Self-Check

- [ ] ls -la output mein permissions read kar sakta/sakti hoon
- [ ] chmod numeric method use kar sakta/sakti hoon (755, 644, 600)
- [ ] chmod symbolic method use kar sakta/sakti hoon (u+x, g-w)
- [ ] chown se ownership change kar sakta/sakti hoon
- [ ] sudo commands run kar sakta/sakti hoon
- [ ] .env file ko secure (600) rakhna jaanta/jaanti hoon

**Next Module:** `04-System` → System management seekho! ⚙️
