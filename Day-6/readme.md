# 🐧 Day 6 — Networking & SSH

> **Goal:** Understand Linux networking basics, connect to remote servers securely with SSH, transfer files, and troubleshoot network issues from the terminal.

![Day](https://img.shields.io/badge/Day-6%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Intermediate-orange?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 5](#1-quick-recap-of-day-5)
2. [How Linux Networking Works](#2-how-linux-networking-works)
3. [ip — View Network Interfaces](#3-ip--view-network-interfaces)
4. [ping — Test Connectivity](#4-ping--test-connectivity)
5. [curl — Make HTTP Requests](#5-curl--make-http-requests)
6. [wget — Download Files](#6-wget--download-files)
7. [ss & netstat — View Open Ports](#7-ss--netstat--view-open-ports)
8. [nmap — Scan Networks](#8-nmap--scan-networks)
9. [DNS Tools — dig & nslookup](#9-dns-tools--dig--nslookup)
10. [SSH — Secure Shell](#10-ssh--secure-shell)
11. [SSH Keys — Passwordless Login](#11-ssh-keys--passwordless-login)
12. [SCP — Copy Files Over SSH](#12-scp--copy-files-over-ssh)
13. [rsync — Efficient File Sync](#13-rsync--efficient-file-sync)
14. [SSH Config File](#14-ssh-config-file)
15. [Firewall Basics — ufw](#15-firewall-basics--ufw)
16. [Daily Tasks Checklist](#-daily-tasks-checklist)
17. [Practice Exercises](#-practice-exercises)
18. [Summary](#-summary)

---

## 1. Quick Recap of Day 5

Make sure you remember these from Day 5:

```bash
ps aux              # List all running processes
top / htop          # Real-time process monitor
kill <PID>          # Kill a process
df -h               # Disk space usage
free -h             # Memory usage
sudo apt install    # Install a package
sudo apt update     # Update package list
```

Today we go online — connecting to remote servers, downloading files, and understanding how Linux talks to the network.

---

## 2. How Linux Networking Works

Before diving into commands, let's understand the core concepts.

### Key Networking Concepts

```
Your Computer
     │
     ├── Network Interface (eth0, wlan0, enp0s3)
     │        │
     │        ├── IP Address     → Your address on the network (e.g. 192.168.1.10)
     │        ├── Subnet Mask    → Defines your network range
     │        └── MAC Address    → Hardware address of your network card
     │
     ├── Gateway (Router)        → Your door to the internet (e.g. 192.168.1.1)
     │
     └── DNS Server              → Translates names to IPs (e.g. 8.8.8.8)
```

### Types of IP Addresses

| Type | Range | Usage |
|------|-------|-------|
| **Loopback** | `127.0.0.1` | Your own machine (localhost) |
| **Private** | `192.168.x.x`, `10.x.x.x` | Local network (home/office) |
| **Public** | Everything else | Internet-facing address |

### Common Network Interfaces

| Interface | Meaning |
|-----------|---------|
| `lo` | Loopback (127.0.0.1 — always present) |
| `eth0` / `enp0s3` | Wired Ethernet |
| `wlan0` / `wlp2s0` | Wireless (WiFi) |
| `docker0` | Docker virtual network |
| `tun0` | VPN tunnel |

---

## 3. ip — View Network Interfaces

`ip` is the modern command for viewing and managing network configuration.

### Show All Network Interfaces

```bash
ip a
# or
ip addr
# or
ip address
```

**Output:**
```
1: lo: <LOOPBACK,UP> mtu 65536
    inet 127.0.0.1/8 scope host lo

2: enp0s3: <BROADCAST,MULTICAST,UP> mtu 1500
    inet 192.168.1.10/24 brd 192.168.1.255 scope global enp0s3
    link/ether 08:00:27:4b:c5:d1 brd ff:ff:ff:ff:ff:ff
```

Reading the output:

```
inet 192.168.1.10/24
│    │              │
│    │              └── /24 = subnet mask (255.255.255.0)
│    └── Your IP address
└── IPv4 address label
```

### Show Only a Specific Interface

```bash
ip a show eth0          # Show only eth0
ip a show wlan0         # Show only WiFi
```

### Show the Routing Table

```bash
ip r
# or
ip route
```

**Output:**
```
default via 192.168.1.1 dev enp0s3    ← your default gateway (router)
192.168.1.0/24 dev enp0s3            ← your local network range
```

### Show Neighbour (ARP) Table

```bash
ip neigh                # Show devices on your local network
```

### Useful ip Commands

```bash
ip link show            # Show network interfaces (link layer)
ip -s link              # Show interface statistics (bytes sent/received)
ip -4 a                 # Show only IPv4 addresses
ip -6 a                 # Show only IPv6 addresses
```

---

## 4. ping — Test Connectivity

`ping` sends **ICMP echo requests** to a host and measures round-trip time. It's the first tool you use to diagnose network issues.

### Syntax

```bash
ping hostname_or_ip
```

### Basic Examples

```bash
ping google.com             # Ping Google (press Ctrl+C to stop)
ping 8.8.8.8                # Ping Google's DNS server by IP
ping 127.0.0.1              # Ping yourself (loopback)
ping 192.168.1.1            # Ping your router/gateway
```

**Output:**
```
PING google.com (142.250.80.46) 56(84) bytes of data.
64 bytes from 142.250.80.46: icmp_seq=1 ttl=118 time=12.4 ms
64 bytes from 142.250.80.46: icmp_seq=2 ttl=118 time=11.8 ms
64 bytes from 142.250.80.46: icmp_seq=3 ttl=118 time=12.1 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
rtt min/avg/max = 11.8/12.1/12.4 ms
```

Reading the output:

| Field | Meaning |
|-------|---------|
| `icmp_seq` | Sequence number |
| `ttl` | Time To Live — hops remaining |
| `time` | Round-trip time in milliseconds |
| `packet loss` | % of lost packets (0% = healthy) |

### Useful ping Options

```bash
ping -c 4 google.com        # Send exactly 4 packets then stop
ping -c 4 -i 2 google.com   # Send 4 packets, 2 seconds apart
ping -s 1000 google.com     # Send larger packets (1000 bytes)
ping -t 5 google.com        # Set TTL to 5
```

### Diagnosing with ping

```bash
# Step 1 — Test loopback (is TCP/IP working?)
ping -c 2 127.0.0.1

# Step 2 — Test your router (is your local network working?)
ping -c 2 192.168.1.1

# Step 3 — Test the internet by IP (is internet reachable?)
ping -c 2 8.8.8.8

# Step 4 — Test DNS (is name resolution working?)
ping -c 2 google.com
```

If Step 3 works but Step 4 fails → **DNS problem**.
If Step 2 fails → **Local network problem**.

---

## 5. curl — Make HTTP Requests

`curl` transfers data to/from servers. It supports HTTP, HTTPS, FTP, and more. It's used by developers constantly.

### Syntax

```bash
curl [options] URL
```

### Basic GET Request

```bash
curl https://example.com            # Fetch a webpage
curl https://api.github.com         # Call an API
```

### Save Output to a File

```bash
curl -o index.html https://example.com       # Save as index.html
curl -O https://example.com/file.zip         # Save with original filename
```

### Follow Redirects

```bash
curl -L https://example.com         # Follow HTTP redirects (301, 302)
```

### Show HTTP Headers Only

```bash
curl -I https://example.com         # Show response headers only
```

**Output:**
```
HTTP/2 200
content-type: text/html; charset=UTF-8
date: Thu, 14 May 2026 09:00:00 GMT
server: nginx
```

### Send Data (POST Request)

```bash
curl -X POST https://api.example.com/data \
     -H "Content-Type: application/json" \
     -d '{"name": "Alice", "age": 25}'
```

### Authentication

```bash
curl -u username:password https://api.example.com
curl -H "Authorization: Bearer your_token" https://api.example.com
```

### Useful curl Options

```bash
curl -v URL             # Verbose — show full request/response
curl -s URL             # Silent — no progress bar
curl -w "%{http_code}" URL   # Show HTTP status code
curl --max-time 10 URL  # Timeout after 10 seconds
curl -k URL             # Skip SSL certificate verification
```

### Quick API Test

```bash
# Get your public IP address
curl https://ipinfo.io/ip

# Get full IP info as JSON
curl https://ipinfo.io/json

# Get weather (wttr.in is a fun terminal weather service)
curl wttr.in/Dhaka
```

---

## 6. wget — Download Files

`wget` is designed specifically for **downloading files** from the internet. It's simpler than curl for downloads.

### Syntax

```bash
wget URL
```

### Basic Download

```bash
wget https://example.com/file.zip           # Download a file
wget https://example.com/image.png          # Download an image
```

### Download with a Custom Name

```bash
wget -O myfile.zip https://example.com/file.zip
```

### Download Multiple Files

```bash
wget https://example.com/file1.zip https://example.com/file2.zip
```

### Resume an Interrupted Download

```bash
wget -c https://example.com/largefile.zip   # Continue where it left off
```

### Download in Background

```bash
wget -b https://example.com/largefile.zip   # Download in background
tail -f wget-log                             # Monitor progress
```

### Download an Entire Website (Mirror)

```bash
wget --mirror --convert-links -P ./website https://example.com
```

### curl vs wget

| Feature | curl | wget |
|---------|------|------|
| Download files | ✅ | ✅ |
| API requests | ✅ | ⚠️ |
| POST/PUT/DELETE | ✅ | ❌ |
| Resume downloads | ❌ | ✅ |
| Mirror websites | ❌ | ✅ |
| Pipe to commands | ✅ | ⚠️ |

---

## 7. ss & netstat — View Open Ports

These commands show **network connections and open ports** on your system.

### `ss` — The Modern Tool

`ss` (socket statistics) replaces the older `netstat`.

```bash
ss -tulpn
```

Breaking down the flags:

| Flag | Meaning |
|------|---------|
| `-t` | Show TCP connections |
| `-u` | Show UDP connections |
| `-l` | Show only listening ports |
| `-p` | Show which process owns the port |
| `-n` | Show numeric (don't resolve hostnames) |

**Output:**
```
Netid  State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port  Process
tcp    LISTEN  0       128     0.0.0.0:22           0.0.0.0:*          sshd
tcp    LISTEN  0       128     0.0.0.0:80           0.0.0.0:*          nginx
tcp    LISTEN  0       128     127.0.0.1:5432       0.0.0.0:*          postgres
```

Reading this:
- Port **22** is open — SSH is running
- Port **80** is open — a web server is running
- Port **5432** only on localhost — PostgreSQL (not exposed to internet)

### More ss Examples

```bash
ss -an                  # All connections, numeric
ss -t state established # Only established TCP connections
ss -s                   # Summary statistics
ss -p | grep firefox    # Show ports used by Firefox
```

### `netstat` — The Classic Tool

```bash
netstat -tulpn          # Same as ss -tulpn
netstat -an             # All connections
netstat -rn             # Routing table
```

> 💡 `ss` is faster and more modern. Use it over `netstat`.

---

## 8. nmap — Scan Networks

`nmap` (Network Mapper) scans hosts to find open ports and services. Used in networking and security.

### Install nmap

```bash
sudo apt install nmap
```

### Basic Scan

```bash
nmap 192.168.1.1            # Scan a single host
nmap scanme.nmap.org        # Scan a hostname (safe test target)
```

**Output:**
```
PORT    STATE  SERVICE
22/tcp  open   ssh
80/tcp  open   http
443/tcp open   https
```

### Useful nmap Scans

```bash
nmap -sV 192.168.1.1            # Detect service versions
nmap -O 192.168.1.1             # Detect operating system
nmap -p 22,80,443 192.168.1.1   # Scan specific ports only
nmap -p 1-1000 192.168.1.1      # Scan first 1000 ports
nmap 192.168.1.0/24             # Scan entire local network
nmap -sn 192.168.1.0/24         # Ping scan — find live hosts only
```

> ⚠️ Only scan networks and hosts you own or have permission to scan.

---

## 9. DNS Tools — dig & nslookup

**DNS (Domain Name System)** translates domain names into IP addresses.

### `dig` — DNS Lookup

```bash
dig google.com                  # Full DNS query for google.com
dig google.com A                # Get IPv4 address (A record)
dig google.com MX               # Get mail server (MX record)
dig google.com NS               # Get nameservers
dig @8.8.8.8 google.com         # Query using Google's DNS server
```

**Output:**
```
;; ANSWER SECTION:
google.com.     299  IN  A  142.250.80.46
```

### Short Answer Only

```bash
dig +short google.com           # Just the IP address
dig +short google.com MX        # Just the mail servers
```

### `nslookup` — Simple DNS Lookup

```bash
nslookup google.com             # Lookup IP for a domain
nslookup 8.8.8.8                # Reverse lookup — IP to domain
```

### Check Your DNS Configuration

```bash
cat /etc/resolv.conf            # See your configured DNS servers
```

**Output:**
```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

---

## 10. SSH — Secure Shell

**SSH (Secure Shell)** lets you log into and control a remote Linux machine securely over a network. All traffic is **encrypted**.

```
Your Computer  ──── encrypted tunnel ────  Remote Server
   (client)          port 22 (SSH)           (server)
```

### Basic SSH Connection

```bash
ssh username@hostname_or_ip
```

**Examples:**

```bash
ssh alice@192.168.1.50              # Connect by IP address
ssh alice@server.example.com        # Connect by hostname
ssh root@10.0.0.1                   # Connect as root (avoid if possible)
```

When you connect for the first time:

```
The authenticity of host '192.168.1.50' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)? yes
```

Type `yes` — this saves the server's fingerprint to `~/.ssh/known_hosts`.

### Connect on a Different Port

SSH uses port **22** by default. Some servers use a different port for security:

```bash
ssh -p 2222 alice@192.168.1.50      # Connect on port 2222
```

### Run a Command on the Remote Server Without Interactive Login

```bash
ssh alice@192.168.1.50 "ls -la"
ssh alice@192.168.1.50 "df -h"
ssh alice@192.168.1.50 "sudo apt update"
```

### Disconnect from SSH

```bash
exit
# or press Ctrl+D
```

---

## 11. SSH Keys — Passwordless Login

Typing a password every time is slow and less secure. **SSH keys** let you authenticate without a password using cryptography.

### How SSH Keys Work

```
Your Computer                    Remote Server
┌─────────────┐                 ┌─────────────┐
│ Private Key │  ─── proves ──► │ Public Key  │
│ (~/.ssh/    │                 │ (authorized │
│  id_ed25519)│                 │  _keys file)│
└─────────────┘                 └─────────────┘
  NEVER share                    Safe to share
```

### Step 1 — Generate an SSH Key Pair

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

**Output:**
```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/yourname/.ssh/id_ed25519): [press Enter]
Enter passphrase (empty for no passphrase): [optional password]
Enter same passphrase again:
Your identification has been saved in /home/yourname/.ssh/id_ed25519
Your public key has been saved in /home/yourname/.ssh/id_ed25519.pub
```

Two files are created:

| File | Type | Description |
|------|------|-------------|
| `~/.ssh/id_ed25519` | Private key | **Never share this** |
| `~/.ssh/id_ed25519.pub` | Public key | Copy this to remote servers |

### View Your Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

**Output:**
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBxxxxxxxxxxxxxxxxxxxxxxxx your_email@example.com
```

### Step 2 — Copy Your Public Key to the Remote Server

#### Method A — `ssh-copy-id` (easiest)

```bash
ssh-copy-id alice@192.168.1.50
```

This automatically adds your public key to the remote server's `~/.ssh/authorized_keys` file.

#### Method B — Manual Copy

If `ssh-copy-id` isn't available:

```bash
# Copy the public key content
cat ~/.ssh/id_ed25519.pub

# SSH into the remote server with password
ssh alice@192.168.1.50

# On the remote server, add the key
mkdir -p ~/.ssh
echo "paste_your_public_key_here" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
exit
```

### Step 3 — Connect Without a Password

```bash
ssh alice@192.168.1.50
# No password prompt! You're in immediately.
```

### Manage Multiple Keys

```bash
# List your keys
ls ~/.ssh/

# Generate a second key for a different server
ssh-keygen -t ed25519 -f ~/.ssh/id_work -C "work server"

# Use a specific key
ssh -i ~/.ssh/id_work alice@work-server.com
```

---

## 12. SCP — Copy Files Over SSH

`scp` (Secure Copy) copies files between your computer and a remote server over SSH. It's encrypted, just like SSH.

### Syntax

```bash
# Copy TO remote server
scp local_file username@host:/remote/path

# Copy FROM remote server
scp username@host:/remote/file local_path
```

### Copy a File TO the Remote Server

```bash
scp report.txt alice@192.168.1.50:/home/alice/
scp report.txt alice@192.168.1.50:~/Documents/
scp report.txt alice@192.168.1.50:~/backup_report.txt    # Copy + rename
```

### Copy a File FROM the Remote Server

```bash
scp alice@192.168.1.50:/home/alice/report.txt .           # Copy here
scp alice@192.168.1.50:~/report.txt ./downloads/          # Copy to folder
```

### Copy a Folder (Recursive)

```bash
scp -r projects/ alice@192.168.1.50:~/                    # Upload folder
scp -r alice@192.168.1.50:~/projects/ ./backup/           # Download folder
```

### Use a Specific Port

```bash
scp -P 2222 file.txt alice@192.168.1.50:~/                # Note: uppercase -P
```

### Useful scp Options

```bash
scp -v file.txt alice@host:~/      # Verbose output
scp -C file.txt alice@host:~/      # Compress during transfer
scp -l 1000 file.txt alice@host:~/ # Limit bandwidth to 1000 Kbit/s
```

---

## 13. rsync — Efficient File Sync

`rsync` is smarter than `scp` — it only transfers **changed parts** of files, making it much faster for large or repeated transfers.

### Install rsync

```bash
sudo apt install rsync
```

### Basic Sync

```bash
rsync -av source/ destination/          # Sync locally
rsync -av projects/ alice@host:~/projects/   # Sync to remote
rsync -av alice@host:~/projects/ ./backup/   # Sync from remote
```

Flag breakdown:

| Flag | Meaning |
|------|---------|
| `-a` | Archive mode (preserves permissions, timestamps, symlinks) |
| `-v` | Verbose output |
| `-z` | Compress data during transfer |
| `-P` | Show progress + allow resume |
| `--delete` | Delete files on destination that don't exist in source |
| `--dry-run` | Simulate without actually copying |

### Recommended Flags for Most Use Cases

```bash
rsync -avzP source/ alice@host:~/destination/
```

### Dry Run First (Safe Preview)

```bash
rsync -avz --dry-run projects/ alice@host:~/projects/
# Shows what WOULD be transferred without doing it
```

### Keep Backup of Deleted Files

```bash
rsync -av --backup --backup-dir=~/backup source/ dest/
```

### rsync vs scp

| Feature | scp | rsync |
|---------|-----|-------|
| Simple copy | ✅ | ✅ |
| Only transfer changes | ❌ | ✅ |
| Resume interrupted | ❌ | ✅ |
| Show progress | ❌ | ✅ |
| Sync directories | ❌ | ✅ |
| Speed on large files | Slower | Faster |

---

## 14. SSH Config File

Typing `ssh alice@192.168.1.50 -p 2222 -i ~/.ssh/id_work` every time is tedious. The **SSH config file** lets you create shortcuts.

### Create or Edit the Config File

```bash
nano ~/.ssh/config
```

### Example Config File

```
# Work server
Host work
    HostName 192.168.1.50
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_work

# Personal VPS
Host myvps
    HostName server.example.com
    User root
    IdentityFile ~/.ssh/id_ed25519

# GitHub
Host github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
```

### Now Connect with Just the Shortcut

```bash
ssh work           # Instead of: ssh alice@192.168.1.50 -p 2222 -i ~/.ssh/id_work
ssh myvps          # Instead of: ssh root@server.example.com
scp file.txt work:~/   # Works with scp too!
```

### Set Correct Permissions

```bash
chmod 600 ~/.ssh/config
```

---

## 15. Firewall Basics — ufw

**ufw** (Uncomplicated Firewall) is the beginner-friendly firewall tool on Ubuntu/Debian. It controls which ports are open or closed.

### Check Firewall Status

```bash
sudo ufw status
sudo ufw status verbose
```

### Enable / Disable the Firewall

```bash
sudo ufw enable             # Turn on firewall
sudo ufw disable            # Turn off firewall
```

### Allow and Deny Ports

```bash
sudo ufw allow 22           # Allow SSH (port 22)
sudo ufw allow 80           # Allow HTTP
sudo ufw allow 443          # Allow HTTPS
sudo ufw allow 8080         # Allow a custom port
sudo ufw deny 23            # Block Telnet
```

### Allow by Service Name

```bash
sudo ufw allow ssh          # Same as allow 22
sudo ufw allow http         # Same as allow 80
sudo ufw allow https        # Same as allow 443
```

### Allow from a Specific IP

```bash
sudo ufw allow from 192.168.1.100           # Allow all traffic from one IP
sudo ufw allow from 192.168.1.0/24          # Allow entire subnet
sudo ufw allow from 192.168.1.100 to any port 22   # Allow SSH from one IP only
```

### Delete a Rule

```bash
sudo ufw delete allow 80
sudo ufw status numbered            # Show rules with numbers
sudo ufw delete 2                   # Delete rule number 2
```

### Reset Firewall

```bash
sudo ufw reset              # Remove all rules and disable
```

---

## ✅ Daily Tasks Checklist

- [ ] Run `ip a` and identify your IP address and network interface
- [ ] Run `ip r` and find your default gateway
- [ ] Ping Google: `ping -c 4 google.com`
- [ ] Ping your router: `ping -c 4 192.168.1.1`
- [ ] Run `curl https://ipinfo.io/ip` to find your public IP
- [ ] Download a file using `wget`
- [ ] Run `ss -tulpn` and see which ports are open
- [ ] Use `dig google.com` to do a DNS lookup
- [ ] Generate an SSH key pair with `ssh-keygen -t ed25519`
- [ ] View your public key with `cat ~/.ssh/id_ed25519.pub`
- [ ] Connect to a remote server via SSH (or practice locally)
- [ ] Copy a file using `scp`
- [ ] Create a `~/.ssh/config` shortcut for a server
- [ ] Check firewall status with `sudo ufw status`

---

## 🏋️ Practice Exercises

### Exercise 1 — Network Diagnostics

```bash
# Step 1: Check your network interfaces
ip a

# Step 2: Check your routing table
ip r

# Step 3: Check local network connectivity
ping -c 3 192.168.1.1

# Step 4: Check internet connectivity
ping -c 3 8.8.8.8

# Step 5: Check DNS resolution
ping -c 3 google.com

# Step 6: Find your public IP
curl -s https://ipinfo.io/ip

# Step 7: Check open ports on your machine
ss -tulpn
```

---

### Exercise 2 — curl API Practice

```bash
# Get your current IP info
curl https://ipinfo.io/json

# Fetch a webpage and save it
curl -o google.html https://www.google.com

# Check HTTP response code
curl -o /dev/null -s -w "%{http_code}\n" https://google.com

# Get terminal weather for Dhaka
curl wttr.in/Dhaka?format=3

# Fetch a sample REST API
curl https://jsonplaceholder.typicode.com/posts/1
```

---

### Exercise 3 — SSH Key Setup

```bash
# Step 1: Generate a new key pair
ssh-keygen -t ed25519 -C "practice@linux.local" -f ~/.ssh/id_practice

# Step 2: View the generated files
ls -la ~/.ssh/

# Step 3: View your public key
cat ~/.ssh/id_practice.pub

# Step 4: View your private key (never share this)
ls -la ~/.ssh/id_practice

# Step 5: If you have a remote server, copy the key
# ssh-copy-id -i ~/.ssh/id_practice.pub user@remote-server

# Step 6: Verify permissions are correct
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_practice
chmod 644 ~/.ssh/id_practice.pub
ls -la ~/.ssh/
```

---

### Exercise 4 — SSH Config File Setup

```bash
# Create the SSH config file
mkdir -p ~/.ssh
cat > ~/.ssh/config << EOF
# Example shortcut (replace with real values)
Host myserver
    HostName 192.168.1.50
    User yourname
    Port 22
    IdentityFile ~/.ssh/id_ed25519

Host github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
EOF

# Set correct permissions
chmod 600 ~/.ssh/config

# View the config
cat ~/.ssh/config
```

---

### Exercise 5 — File Transfer Practice

```bash
# Create a test file to transfer
echo "This file was created on $(date)" > transfer_test.txt
echo "Hostname: $(hostname)" >> transfer_test.txt
echo "User: $(whoami)" >> transfer_test.txt

cat transfer_test.txt

# If you have a remote server:
# scp transfer_test.txt user@192.168.1.50:~/
# scp user@192.168.1.50:~/transfer_test.txt ./received_file.txt

# Practice rsync locally (sync one folder to another)
mkdir -p ~/source_folder ~/dest_folder
echo "file 1" > ~/source_folder/file1.txt
echo "file 2" > ~/source_folder/file2.txt

rsync -av ~/source_folder/ ~/dest_folder/
ls ~/dest_folder/
```

---

### Exercise 6 — DNS Investigation

```bash
# Look up a domain
dig google.com

# Get just the IP
dig +short google.com

# Look up mail servers
dig +short google.com MX

# Look up nameservers
dig +short google.com NS

# Use a different DNS server
dig @1.1.1.1 google.com

# Reverse lookup (IP to domain)
dig -x 8.8.8.8

# Check your system's DNS config
cat /etc/resolv.conf
```

---

## 📝 Summary

Today you learned:

| Command | What It Does |
|---------|-------------|
| `ip a` | Show IP addresses and interfaces |
| `ip r` | Show routing table and gateway |
| `ping -c 4 host` | Test connectivity to a host |
| `curl URL` | Make HTTP requests, fetch data from APIs |
| `curl -O URL` | Download a file with original name |
| `wget URL` | Download files from the internet |
| `wget -c URL` | Resume an interrupted download |
| `ss -tulpn` | Show open/listening ports |
| `nmap host` | Scan ports on a host |
| `dig domain` | DNS lookup for a domain |
| `dig +short domain` | Quick DNS answer only |
| `ssh user@host` | Connect to a remote server |
| `ssh -p 2222 user@host` | Connect on a custom port |
| `ssh-keygen -t ed25519` | Generate SSH key pair |
| `ssh-copy-id user@host` | Copy public key to server |
| `scp file user@host:~/` | Copy file to remote server |
| `scp user@host:~/file .` | Copy file from remote server |
| `scp -r folder/ user@host:~/` | Copy folder recursively |
| `rsync -avzP src/ dest/` | Sync files efficiently |
| `~/.ssh/config` | Store SSH connection shortcuts |
| `sudo ufw allow 22` | Open a port in the firewall |
| `sudo ufw status` | Check firewall rules |

### Networking Troubleshooting Cheat Sheet

```
Problem                          Command to Run
──────────────────────────────────────────────────
What is my IP?               →   ip a
What is my gateway?          →   ip r
Can I reach the router?      →   ping 192.168.1.1
Can I reach the internet?    →   ping 8.8.8.8
Is DNS working?              →   ping google.com
What is google.com's IP?     →   dig +short google.com
What ports are open?         →   ss -tulpn
What is my public IP?        →   curl https://ipinfo.io/ip
```

---

## 🔜 What's Next — Day 7

Tomorrow is the final day! You will learn **Shell Scripting** — combining everything you have learned into automated bash scripts.

Preview of Day 7 topics:

```bash
#!/bin/bash       # Script header
$variable         # Variables
if/else           # Conditionals
for/while         # Loops
functions         # Reusable code blocks
cron              # Schedule scripts to run automatically
```

---

> 💬 **Stuck?** Run `man ssh`, `man curl`, `man scp`, or `man rsync` for detailed help.
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**Day 6 Complete! 🎉**

⭐ You can now connect to remote servers, transfer files, and troubleshoot networks like a pro!

[← Day 5](./Day5_Processes_and_System.md) | [Back to Learning Plan](./LEARNING_PLAN.md) | [Day 7 →](./Day7_Shell_Scripting.md)

</div>
