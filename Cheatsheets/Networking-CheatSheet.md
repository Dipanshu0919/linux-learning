# 🌐 Networking Cheatsheet — Quick Reference

---

## 🔍 Network Diagnostics

```bash
# IP Addresses:
ip addr show                    # All interfaces
ip a                            # Short form
ip addr show eth0               # Specific interface
hostname -I                     # All IPs quick

# Routing:
ip route show                   # Routing table
ip route | grep default         # Default gateway

# DNS:
cat /etc/resolv.conf            # DNS servers
cat /etc/hosts                  # Local DNS overrides
nslookup google.com             # DNS lookup
dig google.com +short           # Just IP
dig google.com MX               # Mail records
dig @8.8.8.8 google.com        # Use specific DNS server
host google.com                 # Simple lookup

# Connectivity:
ping -c 4 google.com            # Ping (4 packets)
ping -c 4 8.8.8.8               # Ping by IP (no DNS)
traceroute google.com           # Path trace
mtr google.com                  # Better traceroute
curl -v https://google.com      # HTTP test verbose
curl -I https://google.com      # Headers only
curl -s ifconfig.me             # Public IP
wget -q -O- ifconfig.me         # Public IP alternate
```

---

## 🚪 Ports

```bash
# Open ports:
ss -tulpn                       # All listening ports
ss -tulpn | grep :80            # Specific port
sudo netstat -tulpn             # Old way

# Port testing:
nc -zv localhost 80             # Test port open
nc -zv google.com 443           # Remote port test
curl -v telnet://localhost:5000  # HTTP port test

# Process on port:
sudo lsof -i :80                # What's on port 80
sudo lsof -i :5000              # Flask port check

# Common ports:
# 22  = SSH
# 80  = HTTP
# 443 = HTTPS
# 5000= Flask default
# 8000= Django default
# 8080= Alt HTTP
# 5432= PostgreSQL
# 3306= MySQL
# 6379= Redis
# 27017=MongoDB
```

---

## 🔒 SSH Quick Reference

```bash
# Connect:
ssh user@hostname                        # Basic connect
ssh -p 2222 user@hostname               # Custom port
ssh -i ~/.ssh/mykey user@hostname       # Specific key
ssh -v user@hostname                    # Debug mode
ssh -L 3307:localhost:3306 user@srv     # Port forwarding

# Keys:
ssh-keygen -t ed25519 -C "email@example.com"  # Generate
ssh-keygen -t rsa -b 4096 -C "email"          # RSA key
cat ~/.ssh/id_ed25519.pub                       # View public key
ssh-copy-id user@hostname                       # Copy key to server

# Config (~/.ssh/config):
Host myserver
    HostName 192.168.1.100
    User deploy
    Port 22
    IdentityFile ~/.ssh/id_ed25519

# File transfer:
scp file.txt user@host:/path/           # Upload
scp user@host:/path/file.txt ./         # Download
scp -r folder/ user@host:/path/         # Upload folder
rsync -avz ./ user@host:/path/          # Sync (smart)
rsync -avz --delete ./ user@host:/path/ # Mirror
```

---

## 🛡️ Firewall (UFW)

```bash
sudo ufw status                         # Status
sudo ufw status verbose                 # Detailed
sudo ufw enable                         # Enable
sudo ufw disable                        # Disable

# Allow:
sudo ufw allow 22/tcp                   # SSH
sudo ufw allow 80/tcp                   # HTTP
sudo ufw allow 443/tcp                  # HTTPS
sudo ufw allow ssh                      # By name
sudo ufw allow from 192.168.1.0/24     # From subnet
sudo ufw allow from 10.0.0.1 to any port 5432  # Specific IP+port

# Deny:
sudo ufw deny 8080/tcp

# Delete rule:
sudo ufw delete allow 8080/tcp
sudo ufw status numbered               # Show with numbers
sudo ufw delete 3                      # Delete rule 3

# Reset:
sudo ufw --force reset                 # Reset all rules
```

---

## 🌍 HTTP with curl

```bash
# GET:
curl https://api.example.com
curl -s https://api.example.com        # Silent
curl -o file.json https://api.example.com  # Save to file

# POST:
curl -X POST https://api.example.com/data \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'

# Headers:
curl -H "Authorization: Bearer TOKEN" https://api.example.com
curl -H "Accept: application/json" https://api.example.com

# Follow redirects:
curl -L https://example.com

# Show headers:
curl -I https://example.com           # Headers only
curl -v https://example.com           # Full verbose

# Authentication:
curl -u username:password https://api.example.com
```

---

## 📊 Network Monitoring

```bash
# Bandwidth:
sudo apt install nethogs nload iftop
sudo nethogs                    # Per-process bandwidth
sudo nload                      # Interface bandwidth
sudo iftop                      # Per-connection bandwidth

# Connections:
ss -an | grep ESTABLISHED | wc -l  # Active connections
ss -tn state established           # TCP connections
netstat -an | grep :80             # Connections to port 80

# Packets:
sudo tcpdump -i eth0               # Capture packets
sudo tcpdump -i eth0 port 80       # Filter by port
sudo tcpdump -i any host google.com # Filter by host
```

---

## 🔗 Common Network Files

```bash
/etc/hosts          # Local DNS (127.0.0.1 myapp.local)
/etc/resolv.conf    # DNS servers (nameserver 8.8.8.8)
/etc/hostname       # Server hostname
/etc/network/       # Network config
~/.ssh/config       # SSH shortcuts
~/.ssh/known_hosts  # Trusted servers
~/.ssh/authorized_keys  # Authorized public keys
```
