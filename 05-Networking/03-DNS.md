# 🌏 Lesson 3: DNS — Domain Name System

---

## 📚 Learning Objectives

- DNS kya hai aur kaise kaam karta hai
- DNS lookup commands
- /etc/hosts file use karna
- DNS troubleshooting

---

## 🌏 DNS kya hai?

**DNS = Domain Name System = Internet ka Phone Book**

```
Tum likhte ho:   google.com
DNS translate:   google.com  →  142.250.80.46  (IP address)
Browser connect: 142.250.80.46:443
```

### DNS ki zaroorat kyun hai?
```
IP yaad rakhna mushkil hai:   142.250.80.46
Domain yaad rakhna aasaan:    google.com
```

---

## 🔄 DNS Resolution Process

```
1. Browser: "google.com ka IP kya hai?"
2. OS Cache check: Nahi hai
3. /etc/hosts check: Nahi hai
4. Local DNS Server (ISP/Router): 8.8.8.8
5. Root DNS Server: ".com" ka server batao
6. .com DNS Server: "google.com" ka server batao
7. Google's DNS Server: "142.250.80.46" - yahi IP hai!
8. Browser: connect karo 142.250.80.46 se
```

---

## 🔍 DNS Commands

### `nslookup` — DNS Query

```bash
# Basic lookup:
nslookup google.com
nslookup python.org

# Specific DNS server se query:
nslookup google.com 8.8.8.8    # Google DNS use karo

# Reverse lookup (IP se domain):
nslookup 8.8.8.8

# MX records (email servers):
nslookup -type=MX gmail.com
```

### `dig` — Advanced DNS

```bash
# Install karo:
sudo apt-get install dnsutils

# Basic query:
dig google.com
dig +short google.com    # Sirf IP

# Specific record types:
dig A google.com         # IPv4 address
dig AAAA google.com      # IPv6 address
dig MX google.com        # Mail servers
dig NS google.com        # Name servers
dig CNAME www.google.com # Alias record
dig TXT google.com       # Text records (SPF, verification)

# Specific DNS server use karo:
dig @8.8.8.8 google.com  # Google Public DNS
dig @1.1.1.1 google.com  # Cloudflare DNS

# Trace DNS resolution:
dig +trace google.com

# Short format:
dig +short A google.com
```

### `host` — Simple DNS lookup

```bash
host google.com
host -t MX gmail.com    # MX records
host 8.8.8.8            # Reverse lookup
```

---

## 📝 /etc/hosts File — Local DNS

```bash
cat /etc/hosts
```

```
127.0.0.1   localhost
127.0.1.1   mycomputer
::1         localhost ip6-localhost ip6-loopback

# Custom entries:
192.168.1.100   myserver
192.168.1.101   database.local
127.0.0.1       myapp.local
```

### Custom domain locally:
```bash
# Development mein custom domain set karo:
sudo nano /etc/hosts

# Add this line:
127.0.0.1   myflaskapp.local

# Ab ye kaam karega:
# curl http://myflaskapp.local:5000
```

---

## 🔧 DNS Troubleshooting

```bash
# DNS server kya hai?
cat /etc/resolv.conf

# DNS flush karo:
sudo systemd-resolve --flush-caches

# Connectivity test:
ping -c 2 google.com
ping -c 2 8.8.8.8       # Sirf IP - DNS bypass

# DNS slow?
time nslookup google.com
time nslookup google.com 8.8.8.8    # Compare different DNS

# Alternative DNS servers:
# 8.8.8.8        = Google Primary
# 8.8.4.4        = Google Secondary
# 1.1.1.1        = Cloudflare (fastest!)
# 1.0.0.1        = Cloudflare Secondary
# 9.9.9.9        = Quad9 (security focused)
```

---

## 🏋️ Practice

```bash
# 1. Basic DNS lookups
nslookup python.org
dig +short A python.org
host python.org

# 2. /etc/hosts dekho
cat /etc/hosts

# 3. DNS server check
cat /etc/resolv.conf

# 4. Different record types
dig MX python.org
dig NS python.org
dig TXT python.org

# 5. Reverse lookup
dig -x 8.8.8.8 +short
```

---

## 📖 Summary

```
DNS = Domain → IP translation
nslookup domain  = Quick DNS lookup
dig domain       = Detailed DNS info
dig +short       = Just the IP
host domain      = Simple lookup
/etc/hosts       = Local DNS overrides
/etc/resolv.conf = DNS servers config
8.8.8.8          = Google DNS
1.1.1.1          = Cloudflare DNS (fastest)
```

---

**Next:** `05-Networking/04-Curl-Wget.md` → HTTP from terminal! 🌐
