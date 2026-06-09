# 🔒 Lesson 5: SSH — Remote Server ka Superhero!

---

## 📚 Learning Objectives

- SSH kya hai aur kaise kaam karta hai
- SSH se server connect karna
- SSH keys generate karna
- Key-based authentication setup karna
- SSH config file use karna
- SSH tunneling basics

---

## 🔒 SSH kya hai?

**SSH = Secure Shell = Encrypted remote access**

```
Tumhara Computer  ──── Internet (encrypted!) ────  Remote Server
    (Client)                                          (Server)

Bina SSH: Data plain text mein jaata (dangerous!)
SSH ke saath: Data encrypted jaata hai (safe!)
```

### SSH kab use karte hain?
```
VPS/Cloud server manage karna (AWS, Azure, DigitalOcean)
GitHub se code push/pull karna
Remote debugging karna
File transfer karna (SCP/SFTP)
```

---

## ⚡ Basic SSH Usage

```bash
# Syntax:
ssh username@server_ip
ssh username@hostname

# Examples:
ssh ubuntu@192.168.1.100        # Ubuntu server
ssh root@203.45.67.89           # Root user (AVOID!)
ssh deploy@myserver.com         # Domain name se

# Different port (default 22):
ssh -p 2222 user@server.com

# Identity file specify karo:
ssh -i ~/.ssh/my_key user@server.com

# Verbose mode (debugging):
ssh -v user@server.com
ssh -vv user@server.com     # More verbose
```

---

## 🔑 SSH Keys — Password se Better!

### Why SSH Keys?
```
Password: "mypassword123"  ← Hackable, forgettable
SSH Keys:  2048-bit or 4096-bit mathematical key pair ← Very secure!
```

### Key Pair kaise kaam karta hai:
```
Private Key (SECRET!) = ~/.ssh/id_rsa      ← Sirf tumhare paas
Public Key  (SHARE!)  = ~/.ssh/id_rsa.pub  ← Server pe daalo

Login process:
1. Tum: "Connect karna chahta hoon"
2. Server: "Ye challenge solve karo" (public key se encrypt)
3. Tum: Private key se solve karo
4. Server: "Sahi! Aao andar!"
```

### SSH Key Generate Karna:

```bash
# RSA key (4096-bit = strong):
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Ed25519 (modern, faster, equally secure):
ssh-keygen -t ed25519 -C "your_email@example.com"

# Interactive prompts:
# Enter file: Press Enter (default ~/.ssh/id_ed25519)
# Passphrase: Enter karo (recommended!) ya Enter for no passphrase

# Keys dekho:
ls -la ~/.ssh/
# id_ed25519      ← Private key (NEVER SHARE!)
# id_ed25519.pub  ← Public key (server pe daalo)

# Public key dekhna:
cat ~/.ssh/id_ed25519.pub
# ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your_email@example.com
```

---

## 🚀 Key-based Authentication Setup

### Method 1: ssh-copy-id (Easiest!)

```bash
# Server pe public key copy karo:
ssh-copy-id user@server.com
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server.com

# Ab password-less login:
ssh user@server.com
# Direct connect! No password needed.
```

### Method 2: Manual Setup

```bash
# Public key copy karo:
cat ~/.ssh/id_ed25519.pub

# Server pe jao (password se pehli baar):
ssh user@server.com

# Server pe:
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
# Paste karo public key
chmod 600 ~/.ssh/authorized_keys

# Ab test karo local se:
exit
ssh user@server.com  # No password!
```

---

## ⚙️ SSH Config File — Multiple Servers Manage Karo

```bash
# Config file banao:
nano ~/.ssh/config
```

```
# ~/.ssh/config

# Example entries:

Host myserver
    HostName 192.168.1.100
    User ubuntu
    Port 22
    IdentityFile ~/.ssh/id_ed25519

Host production
    HostName prod.mycompany.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/prod_key
    ForwardAgent yes

Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_key
```

```bash
# Config ke saath connect karo (simple!):
ssh myserver           # Full command: ssh -i ~/.ssh/id_ed25519 ubuntu@192.168.1.100 -p 22
ssh production         # Full command: ssh -i ~/.ssh/prod_key -p 2222 deploy@prod.mycompany.com
```

---

## 🌐 GitHub ke saath SSH

```bash
# 1. SSH key generate karo
ssh-keygen -t ed25519 -C "your@email.com"

# 2. Public key copy karo
cat ~/.ssh/id_ed25519.pub

# 3. GitHub pe jao:
# Settings → SSH and GPG keys → New SSH key → Paste karo

# 4. Test karo:
ssh -T git@github.com
# Output: Hi username! You've successfully authenticated...

# 5. Ab HTTPS ki jagah SSH use karo:
git clone git@github.com:username/repo.git
# Ya existing repo ka remote change karo:
git remote set-url origin git@github.com:username/repo.git
```

---

## 🔧 SSH Tunneling

```bash
# Local Port Forwarding:
# Remote server ki MySQL ko locally access karo
ssh -L 3307:localhost:3306 user@server.com
# Ab localhost:3307 = server ka MySQL!

# Remote Port Forwarding:
# Local app ko internet pe expose karo
ssh -R 8080:localhost:5000 user@server.com
# Ab server:8080 = tumhara local:5000!

# Dynamic SOCKS Proxy:
ssh -D 1080 user@server.com
# SOCKS proxy as localhost:1080
```

---

## 🛡️ SSH Security Best Practices

```bash
# Server pe (after key setup):
sudo nano /etc/ssh/sshd_config

# Recommended settings:
# PasswordAuthentication no    ← Keys only!
# PermitRootLogin no           ← Root direct login band!
# MaxAuthTries 3               ← Brute force protect
# Port 2222                    ← Default port change (optional)

# Apply changes:
sudo systemctl reload sshd
```

---

## 🔧 Troubleshooting

```bash
# "Permission denied (publickey)":
ls -la ~/.ssh/          # Permissions check karo
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

# Wrong key use ho raha hai:
ssh -v user@server.com 2>&1 | grep "identity"
ssh -i ~/.ssh/correct_key user@server.com

# SSH agent:
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_ed25519
ssh-add -l           # Loaded keys dekho
```

---

## 🏋️ Practice (In Codespaces)

```bash
# 1. SSH key generate karo
ssh-keygen -t ed25519 -C "learning@linux.com" -f ~/.ssh/learning_key -N ""

# 2. Keys dekho
ls -la ~/.ssh/
cat ~/.ssh/learning_key.pub

# 3. SSH config setup karo
cat > ~/.ssh/config << 'EOF'
Host localtest
    HostName localhost
    User codespace
    IdentityFile ~/.ssh/learning_key
EOF

chmod 600 ~/.ssh/config

# 4. SSH known_hosts check
cat ~/.ssh/known_hosts 2>/dev/null | head -5

# 5. GitHub SSH setup verify:
# ssh -T git@github.com
# (GitHub pe key add karne ke baad)
```

---

## 📖 Summary

```
ssh user@server       = Connect to server
ssh -p PORT           = Custom port
ssh -i keyfile        = Specific key use
ssh-keygen -t ed25519 = New SSH key pair
ssh-copy-id user@srv  = Copy public key to server
~/.ssh/config         = SSH shortcuts config
~/.ssh/id_ed25519     = Private key (SECRET!)
~/.ssh/id_ed25519.pub = Public key (share!)
~/.ssh/authorized_keys = Server pe approved keys
ssh -L port:host:port = Local port forwarding
ssh -T git@github.com = GitHub connection test
```

---

**Next:** `05-Networking/06-SCP.md` → Files transfer karo! 📁
