# 🚪 Lesson 2: Ports — Services ka Address

---

## 📚 Learning Objectives

- Ports kya hote hain
- Well-known ports
- Open ports check karna
- Firewall basics

---

## 🚪 Port kya hai?

**Port = IP address ka extension — specific service ka address**

```
IP Address  = Building ka address   (192.168.1.100)
Port        = Building mein flat no (port 80 = HTTP)

http://192.168.1.100:8080
             |          |
             IP         Port
```

---

## 📋 Well-Known Ports

```
Port  Protocol  Use
20    FTP       File transfer (data)
21    FTP       File transfer (control)
22    SSH       Secure Shell (remote access)
25    SMTP      Email sending
53    DNS       Domain name resolution
80    HTTP      Web traffic (no encryption)
443   HTTPS     Web traffic (encrypted)
3000  -         Node.js (convention)
3306  MySQL     Database
5000  Flask     Flask development (convention)
5432  PostgreSQL Database
6379  Redis     Cache database
8000  Django    Django development
8080  HTTP-alt  Alternative HTTP
8443  HTTPS-alt Alternative HTTPS
27017 MongoDB   Database
```

---

## 🔍 Open Ports Check Karna

```bash
# ss - Modern (recommended):
ss -tulpn

# Flags:
# t = TCP connections
# u = UDP connections
# l = Listening ports (open)
# p = Process name
# n = Numeric (IP/port numbers, not names)

# Example output:
# Netid  State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
# tcp    LISTEN  0       128     0.0.0.0:22           0.0.0.0:*      users:(("sshd",pid=892))
# tcp    LISTEN  0       100     0.0.0.0:80           0.0.0.0:*      users:(("nginx",pid=1234))
# tcp    LISTEN  0       5       127.0.0.1:5000       0.0.0.0:*      users:(("python3",pid=5678))

# Specific port check:
ss -tulpn | grep :80
ss -tulpn | grep :5000

# netstat (old):
sudo netstat -tulpn

# Specific port ka process dhundho:
sudo lsof -i :80
sudo lsof -i :5000

# All processes aur unke ports:
sudo lsof -i
```

---

## 🔧 Port Testing

```bash
# Port khula hai ya nahi:
nc -zv localhost 80          # Netcat
nc -zv 192.168.1.1 22        # Remote SSH check
nc -zv -w 3 google.com 443   # 3 second timeout

# Curl se port check:
curl -v telnet://localhost:5000  # (escape karo Ctrl+])

# Python se port check:
python3 -c "import socket; s=socket.socket(); print('Open' if s.connect_ex(('localhost',5000))==0 else 'Closed'); s.close()"
```

---

## 🛡️ Firewall — `ufw` (Uncomplicated Firewall)

```bash
# UFW status:
sudo ufw status
sudo ufw status verbose

# Enable/disable:
sudo ufw enable
sudo ufw disable

# Rules add karo:
sudo ufw allow 22/tcp      # SSH allow karo
sudo ufw allow 80/tcp      # HTTP allow
sudo ufw allow 443/tcp     # HTTPS allow
sudo ufw allow 5000/tcp    # Flask dev allow

# Service naam se:
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Deny karo:
sudo ufw deny 8080/tcp

# Specific IP se allow:
sudo ufw allow from 192.168.1.0/24 to any port 5432  # PostgreSQL sirf local network se

# Rule delete:
sudo ufw delete allow 8080/tcp

# Rules list:
sudo ufw status numbered
sudo ufw delete 3          # Rule number 3 delete
```

---

## 🏋️ Practice

```bash
# 1. Open ports dekho
ss -tulpn

# 2. Flask test karo
python3 -m http.server 8080 &
ss -tulpn | grep :8080
kill %1    # Band karo

# 3. Port listening info
sudo lsof -i :22 2>/dev/null || echo "Port 22 check done"

# 4. UFW status (informational)
sudo ufw status 2>/dev/null || echo "UFW not available in Codespaces"
```

---

## 📖 Summary

```
Port 22   = SSH
Port 80   = HTTP
Port 443  = HTTPS
Port 5000 = Flask (default)
Port 5432 = PostgreSQL

ss -tulpn          = Open ports dekho
lsof -i :PORT      = Specific port info
nc -zv host PORT   = Port test karo
ufw allow PORT     = Firewall mein allow
ufw status         = Firewall status
```

---

**Next:** `05-Networking/03-DNS.md` → DNS system samjho! 🌏
