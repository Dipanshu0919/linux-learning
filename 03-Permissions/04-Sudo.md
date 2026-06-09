# 🔑 Lesson 4: Sudo — Admin Powers Safely Use Karna

---

## 📚 Learning Objectives

- Sudo kya hai aur kyun zaruri hai
- Sudo commands use karna
- Sudoers file samjhna
- Sudo security best practices

---

## 🔑 Sudo kya hai?

**Sudo = Superuser Do**

Regular user hoke root/admin commands run karne ka tarika!

```
BINA SUDO:  user → limited access
SUDO ke saath: user → root powers (temporarily!)
```

### Analogy:
> Sochho sudo ek master key ki tarah hai. Tumhare paas apni key hai (regular user), par building ke kisi bhi room mein jaane ke liye manager (sudo) se permission leni padti hai.

---

## ⚡ Sudo Commands

```bash
# Basic sudo:
sudo command
sudo apt-get update

# Root shell:
sudo bash
sudo su -

# Specific user ke saath:
sudo -u www-data python3 app.py

# Sudo without password (sudoers mein configured ho):
# (Automatic hota hai Codespaces mein)

# Sudo ka cache clear:
sudo -k

# Sudo version:
sudo -V
```

---

## 📋 Common Sudo Use Cases

```bash
# Package install:
sudo apt-get install nginx
sudo apt-get install python3-pip

# System files edit:
sudo nano /etc/hosts
sudo nano /etc/nginx/nginx.conf

# Services manage:
sudo systemctl start nginx
sudo systemctl restart flask_app

# File ownership:
sudo chown www-data:www-data /var/www/html/

# Protected folders:
sudo ls /root/
sudo cat /etc/shadow

# Log files read:
sudo cat /var/log/auth.log
sudo journalctl -f
```

---

## 📁 Sudoers File

```bash
# Sudoers file dekho (SAFELY):
sudo visudo
# Ya:
sudo cat /etc/sudoers
```

### Format:
```
# user/group  hosts = (run_as) commands
codespace  ALL=(ALL:ALL) NOPASSWD:ALL  # Codespaces mein ye hota hai!
alice      ALL=(ALL) /usr/bin/apt      # Sirf apt chalane ki permission
%sudo      ALL=(ALL:ALL) ALL           # sudo group ke sab members
```

### Custom sudo rules:
```bash
# Deploy user ko sirf nginx restart ki permission:
sudo visudo
# Add: deploy ALL=(ALL) NOPASSWD: /bin/systemctl restart nginx
```

---

## 🛡️ Security Tips

```bash
# 1. Root se directly kaam karne se bachna:
# GALAT: sudo su aur phir sab kuch root se karna
# SAHI: har command pe sudo lagana

# 2. Sudo history dekho:
sudo cat /var/log/auth.log | grep sudo

# 3. Sab commands sudo se nahi chahiye:
ls, cd, cat, echo  # sudo ki zaroorat NAHI
apt, systemctl, chown  # sudo ki zaroorat hai
```

---

## 🏋️ Practice

```bash
# Current sudo permissions check:
sudo -l

# Sudo se system info:
sudo id
sudo whoami
sudo cat /etc/shadow | head -3

# Package info:
sudo apt list --installed | head -20
```

---

## 📖 Summary

```
sudo cmd         = Run as root (temporary)
sudo -u user cmd = Run as specific user
sudo -l          = Your sudo permissions
sudo visudo      = Edit sudoers file safely
/etc/sudoers     = Permissions file
```

**Next:** `03-Permissions/Exercises.md` → Practice karo!
