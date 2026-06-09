# 🌍 Lesson 4: Domains & DNS Setup

---

## 📚 Learning Objectives

- Domain name kya hai
- DNS records types samjhna
- Domain ko server se point karna
- Subdomain setup karna

---

## 🌍 Domain Name kya hai?

```
Domain Name = Human-readable address for your server

yourdomain.com
    |           |
   name        TLD (Top Level Domain)
```

### Domain Anatomy:
```
sub.domain.com
 |     |    |
 |     |    +-- TLD (.com, .in, .io, .org)
 |     +-- Second Level Domain (your name)
 +-- Subdomain (www, api, mail, etc.)

Examples:
www.google.com       → www subdomain of google.com
api.myapp.com        → api subdomain
mail.company.com     → mail subdomain
```

---

## 📋 DNS Record Types

```
A Record     = Domain → IPv4 address
               mysite.com → 192.168.1.100

AAAA Record  = Domain → IPv6 address
               mysite.com → 2001:db8::1

CNAME Record = Domain alias (Canonical Name)
               www.mysite.com → mysite.com

MX Record    = Mail server
               mail.mysite.com → priority 10

TXT Record   = Text info (SPF, verification)
               "v=spf1 include:sendgrid.net ~all"

NS Record    = Name Servers (who manages DNS)
               ns1.cloudflare.com
               ns2.cloudflare.com

TTL          = Time To Live (seconds, how long cached)
```

---

## 🔧 Domain Configuration Steps

### Step 1: Domain Purchase karo
- Namecheap, GoDaddy, Google Domains, Cloudflare

### Step 2: Name Servers Set Karo
```
Provider pe jao → DNS Settings → Name Servers:
ns1.cloudflare.com
ns2.cloudflare.com
```

### Step 3: A Record Set Karo
```
Cloudflare/Namecheap DNS Panel mein:
Type: A
Name: @ (ya yourdomain.com)
Value: YOUR_SERVER_IP (e.g., 203.45.67.89)
TTL: Auto

www subdomain ke liye:
Type: A
Name: www
Value: YOUR_SERVER_IP
TTL: Auto
```

### Step 4: Propagation Wait karo
```
DNS changes propagate hone mein 15 min - 48 hours lagte hain!

Check karo:
dig yourdomain.com +short
nslookup yourdomain.com 8.8.8.8
```

---

## 🌤️ Cloudflare — Free DNS + CDN + DDoS Protection

```
Cloudflare ke benefits:
- Free SSL (orange cloud enabled)
- DDoS protection
- CDN (Content Delivery Network - speed!)
- Analytics
- Caching
```

### Cloudflare DNS Records:
```
A Record:
Name: @         Value: YOUR_SERVER_IP    Proxy: Orange cloud (yes!)
Name: www       Value: YOUR_SERVER_IP    Proxy: Orange cloud

CNAME:
Name: api       Target: yourdomain.com   Proxy: Orange cloud
```

---

## 🔧 Nginx mein Domain Configure karo

```bash
sudo nano /etc/nginx/sites-available/yourdomain.com
```

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

---

## 🏋️ Practice (No real domain needed)

```bash
# /etc/hosts se local domain simulate karo:
sudo nano /etc/hosts
# Add: 127.0.0.1   myapp.local

# Test:
curl http://myapp.local  # Localhost pe point karega!

# DNS lookup practice:
dig google.com A
dig google.com MX
dig google.com NS
nslookup -type=TXT google.com 8.8.8.8
```

---

## 📖 Summary

```
A Record    = Domain → IP
CNAME       = Alias → another domain
MX Record   = Mail server
TXT Record  = Text (SPF, verification)
TTL         = Cache time (seconds)
Cloudflare  = Free DNS + SSL + CDN
dig domain  = DNS check karo
nslookup    = DNS query
Propagation = 15min - 48 hours
```

**Next:** `07-Web-Servers/05-HTTPS-SSL.md` → SSL setup karo! 🔒
