# 👤 Lesson 1: Users — Linux ka Multi-User System

---

## 📚 Learning Objectives

- Linux user system kya hai
- Users kaise create, modify, delete karein
- User information kaise dekhein
- Password management

---

## 👥 Linux User System

Linux ek **multi-user system** hai — ek computer pe ek saath multiple users ho sakte hain.

```
root user (UID 0)        = System ka boss (Admin)
    |
    |-- System users     = Background services chalate hain
    |   (www-data, nobody, daemon)
    |
    +-- Regular users    = Humans (tum aur mein!)
        (codespace, alice, bob)
```

### Users ki Files:
```bash
cat /etc/passwd     # All users ki info
cat /etc/shadow     # Password hashes (encrypted!)
cat /etc/group      # Groups ki info
```

### `/etc/passwd` format:
```
username:password:UID:GID:comment:home:shell
codespace:x:1000:1000::/home/codespace:/bin/bash
```

---

## 🔑 Important Commands

### `whoami` — Current user
```bash
whoami
# Output: codespace
```

### `id` — User IDs aur Groups
```bash
id
# uid=1000(codespace) gid=1000(codespace) groups=1000(codespace),27(sudo)

id username  # Specific user ki info
```

### `who` — Currently logged in users
```bash
who
# codespace pts/0  Jun 7 10:00 (:0)
```

### `w` — Who is doing what
```bash
w
# Logged in users + unke commands
```

### `last` — Login history
```bash
last
last codespace    # Specific user ki history
```

---

## ➕ User Create Karna

```bash
# Basic user create:
sudo useradd username

# Full options ke saath:
sudo useradd -m -s /bin/bash -c "Full Name" username
# -m = Home directory banao
# -s = Shell specify karo (/bin/bash)
# -c = Comment/Full name

# Password set karo:
sudo passwd username

# Interactive user setup (Recommended):
sudo adduser username    # Puchh puchh ke banataa hai!
```

### Example:
```bash
sudo adduser devuser
# Adding user `devuser' ...
# Adding new group `devuser' (1001) ...
# Adding new user `devuser' (1001) with group `devuser' ...
# Creating home directory `/home/devuser' ...
# New password: [type password]
# Retype new password: [retype]
# passwd: password updated successfully
# Full Name []: Dev User
```

---

## ✏️ User Modify Karna

```bash
# Username change:
sudo usermod -l new_name old_name

# Shell change:
sudo usermod -s /bin/zsh username

# Comment/Full name change:
sudo usermod -c "New Full Name" username

# Home directory change:
sudo usermod -d /new/home -m username

# Account lock karo:
sudo usermod -L username

# Account unlock karo:
sudo usermod -U username

# Expiry date set karo:
sudo usermod -e 2026-12-31 username
```

---

## ❌ User Delete Karna

```bash
# User delete (home directory rakho):
sudo userdel username

# User + home directory delete:
sudo userdel -r username

# Force delete (logged in bhi ho toh):
sudo userdel -rf username
```

---

## 🔐 Password Management

```bash
# Apna password change karo:
passwd

# Doosre ka password change (sudo):
sudo passwd username

# Password expire karo (next login pe change hoga):
sudo passwd -e username

# Password lock karo:
sudo passwd -l username

# Password unlock karo:
sudo passwd -u username

# Password info dekho:
sudo chage -l username
```

---

## 🔄 User Switching

```bash
# User switch karo:
su username
su - username     # Fresh environment ke saath (recommended)

# Root pe jao:
sudo su
sudo su -

# Ek command doosre user se run karo:
sudo -u username command
sudo -u www-data python3 app.py

# Root se wapas aao:
exit
# Ya Ctrl + D
```

---

## 🏋️ Practice

```bash
# Current user info dekho
whoami
id
who

# /etc/passwd ka structure dekho
head -5 /etc/passwd
grep "codespace" /etc/passwd

# Home directories dekho
ls /home

# User ki shell kya hai?
echo $SHELL

# Environment variables
env | grep USER
env | grep HOME
```

---

## 📖 Summary

```
whoami         = Current user naam
id             = UID, GID, groups
who / w        = Logged in users
useradd/adduser = User create
usermod        = User modify
userdel        = User delete
passwd         = Password change
su username    = User switch
/etc/passwd    = All users file
```

---

**Next:** `03-Permissions/02-Groups.md` → Groups seekho! 👥
