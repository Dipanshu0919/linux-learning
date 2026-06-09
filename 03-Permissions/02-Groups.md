# 👥 Lesson 2: Groups — Users ka Organization

---

## 📚 Learning Objectives

- Linux groups kya hote hain
- Groups create aur manage karna
- Users ko groups mein add karna
- Primary vs Secondary groups

---

## 👥 Groups kya hain?

Groups ek tarika hai users ko organize karne ka. Ek file ya folder ke permissions ek puri group ko de sakte ho!

```
Group Example: "developers"
    |-- alice
    |-- bob
    +-- charlie

/var/www/html/  →  group: developers  →  alice, bob, charlie sab access kar sakte hain
```

### Types:
```
Primary Group   = Har user ka main group (username se same hota hai)
Secondary Groups = Extra groups (sudo, docker, www-data, etc.)
```

---

## 🔍 Group Commands

```bash
# All groups dekho
cat /etc/group

# Current user ke groups
groups
id

# Specific user ke groups
groups username
id username
```

### /etc/group format:
```
groupname:password:GID:members
developers:x:1001:alice,bob,charlie
sudo:x:27:codespace
```

---

## ➕ Group Create/Modify/Delete

```bash
# Group banao:
sudo groupadd developers
sudo groupadd -g 1500 devops  # Specific GID ke saath

# User ko group mein add karo:
sudo usermod -aG developers alice     # -a = append (existing groups rakho!)
sudo usermod -aG sudo,docker alice    # Multiple groups

# Primary group change:
sudo usermod -g newgroup username

# Group rename:
sudo groupmod -n new_name old_name

# Group delete:
sudo groupdel groupname
```

**IMPORTANT:** `-aG` mein `-a` mat bhulna! Bina `-a` ke purane groups hatt jaayenge!

---

## 🔒 Group Permissions Example

```bash
# Scenario: Team project folder

# Group banao
sudo groupadd webteam

# Users add karo
sudo usermod -aG webteam alice
sudo usermod -aG webteam bob

# Project folder banao
sudo mkdir /var/www/team_project
sudo chown root:webteam /var/www/team_project
sudo chmod 775 /var/www/team_project
# 775 = rwxrwxr-x
# Owner (root) = full access
# Group (webteam) = full access
# Others = read + execute only

# Ab alice aur bob dono write kar sakte hain!
```

---

## 🏋️ Practice

```bash
# Current groups dekho
groups
id

# /etc/group mein specific groups
grep "sudo" /etc/group
grep "$(whoami)" /etc/group

# System groups count
wc -l /etc/group
```

---

## 📖 Summary

```
groups           = Current user ke groups
id               = User + group info
groupadd name    = Group create
usermod -aG grp  = User ko group mein add (-a mat bhulna!)
groupdel name    = Group delete
/etc/group       = Groups file
```

**Next:** `03-Permissions/03-Permissions.md` → rwx system master karo!
