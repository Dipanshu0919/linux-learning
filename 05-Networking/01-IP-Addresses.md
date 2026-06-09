# 🌐 Lesson 1: IP Addresses — Network ki Duniya

---

## 📚 Learning Objectives

- IP address kya hota hai
- Public vs Private IP
- IPv4 aur IPv6
- Network interfaces check karna
- Basic networking commands

---

## 🌍 IP Address kya hai?

**IP = Internet Protocol Address = Computer ka "phone number"**

Jaise har phone ka number hota hai, har computer (network pe) ka ek IP address hota hai.

```
192.168.1.100  ← Ye ek IP address hai

Format: xxx.xxx.xxx.xxx
        |   |   |   |
       0-255 0-255 0-255 0-255  (Each part = 8 bits)

Total 4 parts = IPv4 = 32-bit address
```

---

## 🏠 Private vs Public IP

```
PUBLIC IP (Internet pe unique):
    → Assigned by ISP
    → Poori internet pe unique hota hai
    → Tu isko jaanne ke liye: curl ifconfig.me

PRIVATE IP (Local network mein):
    → Router assign karta hai
    → Sirf apne network mein kaam karta hai
    → Ranges:
        10.0.0.0    - 10.255.255.255
        172.16.0.0  - 172.31.255.255
        192.168.0.0 - 192.168.255.255

LOOPBACK (Apna hi computer):
    → 127.0.0.1 = localhost
    → Flask development: http://127.0.0.1:5000
```

---

## 🔍 Network Interface Commands

```bash
# Modern way (recommended):
ip addr show
ip a              # Short form

# Old way (still works):
ifconfig          # (apt install net-tools)

# Specific interface:
ip addr show eth0
ip addr show lo    # Loopback interface

# Public IP check:
curl ifconfig.me
curl -4 icanhazip.com
wget -qO- ifconfig.me

# Default route / gateway:
ip route show
ip route | grep default
```

### ip addr output samjhna:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo          ← Loopback IP
    inet6 ::1/128 scope host

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff   ← MAC address
    inet 172.17.0.2/16 scope global eth0                  ← Private IP
    inet6 fe80::42:acff:fe11:2/64 scope link
```

---

## 🔢 Subnet Mask aur CIDR

```
IP: 192.168.1.100/24
            |
            +-- /24 = Subnet mask (CIDR notation)

/24 = 255.255.255.0 = First 24 bits network, last 8 bits host
/16 = 255.255.0.0   = First 16 bits network
/8  = 255.0.0.0     = First 8 bits network

/24 network mein:
    Network:    192.168.1.0
    Usable IPs: 192.168.1.1 - 192.168.1.254 (254 computers!)
    Broadcast:  192.168.1.255
```

---

## 🔗 Connectivity Check karna

```bash
# Ping - Is server reachable?
ping google.com          # Ctrl+C se band karo
ping -c 4 google.com     # Sirf 4 packets
ping -c 4 8.8.8.8        # Google DNS IP se ping

# Traceroute - Route trace karo:
traceroute google.com
mtr google.com           # Better traceroute (sudo apt install mtr)

# DNS lookup:
nslookup google.com
dig google.com

# Port check:
nc -zv google.com 80     # Port 80 open hai?
nc -zv google.com 443    # HTTPS port?
```

---

## 🌐 IPv6

```
IPv6 = 128-bit address (IPv4 = 32-bit)
Format: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
        (8 groups of 4 hexadecimal digits)

Loopback: ::1  (IPv6 mein 127.0.0.1 jaisa)
```

---

## 🏋️ Practice

```bash
# 1. Network interfaces dekho
ip addr show

# 2. Private IP find karo
ip addr | grep "inet " | grep -v "127.0.0.1"

# 3. Routing table
ip route show

# 4. Public IP (internet chahiye)
curl -s ifconfig.me 2>/dev/null && echo "" || echo "No internet (Codespaces mein normal)"

# 5. Ping localhost
ping -c 3 127.0.0.1
ping -c 3 localhost

# 6. DNS test
host google.com 2>/dev/null || nslookup google.com
```

---

## 📖 Summary

```
ip addr show      = Network interfaces + IPs
ifconfig          = Old version
127.0.0.1         = Localhost (apna computer)
ping host         = Connectivity check
traceroute host   = Path trace
curl ifconfig.me  = Public IP check
/24, /16, /8      = Subnet (CIDR notation)
```

---

**Next:** `05-Networking/02-Ports.md` → Ports kya hote hain! 🚪
