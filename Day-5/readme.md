# 🐧 Day 5 — Processes & System

> **Goal:** Understand how Linux manages running programs, monitor system resources, control processes, and manage software with package managers.

![Day](https://img.shields.io/badge/Day-5%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Intermediate-orange?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 4](#1-quick-recap-of-day-4)
2. [What is a Process?](#2-what-is-a-process)
3. [ps — List Running Processes](#3-ps--list-running-processes)
4. [top — Real-Time Process Monitor](#4-top--real-time-process-monitor)
5. [htop — Interactive Process Viewer](#5-htop--interactive-process-viewer)
6. [Foreground & Background Jobs](#6-foreground--background-jobs)
7. [kill — Stop a Process](#7-kill--stop-a-process)
8. [System Resource Monitoring](#8-system-resource-monitoring)
9. [System Information Commands](#9-system-information-commands)
10. [Package Management — apt](#10-package-management--apt)
11. [Package Management — Other Distros](#11-package-management--other-distros)
12. [systemd — Managing Services](#12-systemd--managing-services)
13. [Daily Tasks Checklist](#-daily-tasks-checklist)
14. [Practice Exercises](#-practice-exercises)
15. [Summary](#-summary)

---

## 1. Quick Recap of Day 4

Make sure you remember these from Day 4:

```bash
whoami                  # Current user
id                      # User and group IDs
ls -la                  # View file permissions
chmod 755 file.sh       # Change permissions
chown user:group file   # Change ownership
sudo command            # Run as root
```

Today we shift focus from files to **running programs** — how to see what's happening on your system and take control of it.

---

## 2. What is a Process?

Every time you run a command or open a program, Linux creates a **process** — a running instance of a program.

```
You type: ls
Linux creates → [process: ls, PID: 1234] → runs → prints output → exits
```

### Key Concepts

| Term | Meaning |
|------|---------|
| **PID** | Process ID — unique number assigned to every process |
| **PPID** | Parent Process ID — the process that started this one |
| **UID** | User ID — which user owns the process |
| **TTY** | Terminal the process is attached to |
| **CPU %** | How much processor power the process is using |
| **MEM %** | How much RAM the process is using |
| **State** | What the process is currently doing |

### Process States

| State | Symbol | Meaning |
|-------|--------|---------|
| Running | `R` | Actively using the CPU |
| Sleeping | `S` | Waiting for something (most processes) |
| Stopped | `T` | Paused (e.g. Ctrl+Z) |
| Zombie | `Z` | Finished but not cleaned up yet |
| Idle | `I` | Kernel thread doing nothing |

### The Process Tree

Every process has a parent. It starts from `init` or `systemd` (PID 1):

```
systemd (PID 1)
├── bash (PID 200)
│   └── ls (PID 201)
├── sshd (PID 300)
│   └── bash (PID 301)
└── cron (PID 400)
```

```bash
pstree          # View the full process tree
pstree -p       # Show PIDs in the tree
```

---

## 3. ps — List Running Processes

`ps` (process status) takes a **snapshot** of currently running processes.

### Basic Usage

```bash
ps
```

**Output:**
```
  PID TTY          TIME CMD
  501 pts/0    00:00:00 bash
  523 pts/0    00:00:00 ps
```

This only shows processes **in your current terminal session**.

---

### `ps aux` — Show All Processes

This is the most commonly used form:

```bash
ps aux
```

**Output:**
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1  22520  5632 ?        Ss   09:00   0:01 /sbin/init
root         2  0.0  0.0      0     0 ?        S    09:00   0:00 [kthreadd]
yourname   501  0.0  0.2  25312  9876 pts/0    Ss   09:05   0:00 bash
yourname   523  0.0  0.1  10876  3456 pts/0    R+   09:10   0:00 ps aux
```

### Breaking Down `ps aux` Columns

| Column | Meaning |
|--------|---------|
| `USER` | Who owns the process |
| `PID` | Process ID |
| `%CPU` | CPU usage percentage |
| `%MEM` | Memory usage percentage |
| `VSZ` | Virtual memory size (KB) |
| `RSS` | Real memory used (KB) |
| `TTY` | Terminal (`?` means no terminal) |
| `STAT` | Process state |
| `START` | When it started |
| `TIME` | Total CPU time used |
| `COMMAND` | The command that started it |

---

### Useful `ps` Combinations

```bash
ps aux | grep firefox           # Find a specific process
ps aux | grep -v grep           # Exclude grep itself from results
ps aux --sort=-%cpu | head -10  # Top 10 CPU-hungry processes
ps aux --sort=-%mem | head -10  # Top 10 memory-hungry processes
ps -u yourname                  # Show only your processes
ps -ef                          # Full format with PPID
```

### `ps -ef` — Full Format

```bash
ps -ef
```

**Output:**
```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 09:00 ?        00:00:01 /sbin/init
yourname   501   200  0 09:05 pts/0    00:00:00 bash
yourname   523   501  0 09:10 pts/0    00:00:00 ps -ef
```

> 💡 `PPID` (Parent PID) shows which process launched this one.

---

## 4. top — Real-Time Process Monitor

`top` gives you a **live, auto-refreshing** view of your system — like a task manager in the terminal.

```bash
top
```

**What you see:**

```
top - 09:15:01 up 2:10,  1 user,  load average: 0.12, 0.08, 0.05
Tasks: 142 total,   1 running, 141 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.3 us,  0.7 sy,  0.0 ni, 96.8 id,  0.2 wa
MiB Mem :   7850.0 total,   4210.0 free,   2100.0 used,   1540.0 buff
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  501 yourname  20   0   25312   9876   7654 S   1.3   0.1   0:00.05 bash
 1001 yourname  20   0 1234567 123456  45678 S   5.2   1.6   0:23.12 firefox
```

### Understanding the Top Section

```
up 2:10            → System has been running for 2 hours 10 minutes
load average: 0.12 → Average CPU load (1min, 5min, 15min)
Tasks: 142 total   → Total number of processes
%Cpu: 2.3 us       → 2.3% CPU used by user programs
96.8 id            → 96.8% CPU is idle (doing nothing)
0.2 wa             → 0.2% waiting for disk I/O
MiB Mem: 7850 total → Total RAM
2100.0 used        → RAM currently in use
```

### Navigation Inside `top`

| Key | Action |
|-----|--------|
| `q` | Quit |
| `k` | Kill a process (enter PID) |
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `N` | Sort by PID |
| `T` | Sort by running time |
| `r` | Renice (change priority) |
| `h` | Help |
| `1` | Show individual CPU cores |
| `u` | Filter by username |

---

## 5. htop — Interactive Process Viewer

`htop` is a modern, colorful, mouse-friendly version of `top`. It must be installed first.

```bash
sudo apt install htop      # Install htop
htop                       # Launch it
```

### What Makes htop Better Than top

- Color-coded CPU and memory bars
- Mouse support — click to select and kill processes
- Easier to read and navigate
- Shows process tree view with `F5`

### htop Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `F1` | Help |
| `F2` | Setup / customize |
| `F3` | Search for a process |
| `F4` | Filter processes |
| `F5` | Tree view |
| `F6` | Sort by column |
| `F9` | Kill selected process |
| `F10` | Quit |
| `Space` | Tag a process |
| `u` | Filter by user |

---

## 6. Foreground & Background Jobs

Linux lets you run processes in the **background** so your terminal stays free.

---

### Run a Command in the Background — `&`

Add `&` at the end to run in the background:

```bash
sleep 60 &
```

**Output:**
```
[1] 1234
```

- `[1]` is the **job number**
- `1234` is the **PID**

Your terminal is free immediately while `sleep 60` runs in the background.

---

### `jobs` — List Background Jobs

```bash
jobs
```

**Output:**
```
[1]+  Running    sleep 60 &
[2]-  Stopped    nano file.txt
```

---

### `Ctrl + Z` — Pause (Suspend) a Running Process

While a command is running, press `Ctrl + Z` to **pause** it:

```bash
sleep 120
# Press Ctrl+Z
```

**Output:**
```
[1]+  Stopped    sleep 120
```

The process is paused and moved to the background.

---

### `bg` — Resume a Paused Job in the Background

```bash
bg %1         # Resume job number 1 in background
bg            # Resume the most recently stopped job
```

---

### `fg` — Bring a Background Job to the Foreground

```bash
fg %1         # Bring job 1 to foreground
fg            # Bring the most recent background job forward
```

---

### Full Job Control Workflow

```bash
# Start a long process
sleep 300

# Suspend it
Ctrl+Z        # [1]+ Stopped

# Check jobs
jobs          # [1]+ Stopped  sleep 300

# Send it to background
bg %1         # [1]+ Running  sleep 300 &

# Check again
jobs          # [1]+ Running  sleep 300 &

# Bring back to foreground
fg %1         # sleep 300

# Now it runs in foreground — Ctrl+C to stop it
Ctrl+C
```

---

## 7. kill — Stop a Process

`kill` sends a **signal** to a process — most commonly to stop it.

### Kill by PID

```bash
kill 1234           # Send default signal (SIGTERM — polite stop)
kill -9 1234        # Force kill (SIGKILL — instant, no cleanup)
kill -15 1234       # Same as default (SIGTERM)
```

### Common Signals

| Signal | Number | Name | Meaning |
|--------|--------|------|---------|
| SIGTERM | 15 | Terminate | Politely ask the process to stop |
| SIGKILL | 9 | Kill | Force stop immediately — cannot be ignored |
| SIGHUP | 1 | Hang Up | Reload config (used for daemons) |
| SIGSTOP | 19 | Stop | Pause the process |
| SIGCONT | 18 | Continue | Resume a paused process |

> 💡 Always try `kill PID` (SIGTERM) first. Use `kill -9` only if the process refuses to stop.

---

### `killall` — Kill by Process Name

```bash
killall firefox             # Kill all firefox processes
killall -9 chrome           # Force kill all chrome processes
```

---

### `pkill` — Kill Using Pattern

```bash
pkill firefox               # Kill processes matching "firefox"
pkill -u yourname           # Kill all processes by a user
pkill -9 chrome             # Force kill by pattern
```

---

### Finding the PID First

```bash
ps aux | grep firefox       # Find the PID visually
pgrep firefox               # Print PID of matching process name
pgrep -l firefox            # Print PID + process name
```

---

### `Ctrl + C` — Stop the Current Foreground Process

While a program is running in your terminal:

```bash
ping google.com
# Press Ctrl+C to stop
```

---

## 8. System Resource Monitoring

### `df` — Disk Space Usage

`df` shows how much disk space is used and available on each filesystem.

```bash
df -h
```

**Output:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   18G   30G  38% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
/dev/sda2       200G   90G  100G  47% /home
```

```bash
df -h /         # Check only root partition
df -h /home     # Check home partition
df -i           # Show inode usage instead of space
```

> ⚠️ When `Use%` approaches 90-100%, your system will have serious problems. Clean up!

---

### `du` — Disk Usage of Files and Folders

`du` shows how much space a **specific file or directory** uses.

```bash
du -h file.txt              # Size of a single file
du -h folder/               # Size of a folder and its contents
du -sh folder/              # Summary — total size only
du -sh ~                    # Total size of your home directory
du -sh /*                   # Size of each top-level directory
```

**Output of `du -sh ~/`:**
```
4.2G    /home/yourname/
```

### Find the Biggest Folders

```bash
du -h ~ | sort -rh | head -10
```

---

### `free` — Memory (RAM) Usage

```bash
free -h
```

**Output:**
```
              total        used        free      shared  buff/cache   available
Mem:           7.7G        2.1G        3.8G       234M        1.7G        5.1G
Swap:          2.0G          0B        2.0G
```

| Column | Meaning |
|--------|---------|
| `total` | Total installed RAM |
| `used` | RAM actively in use |
| `free` | Completely unused RAM |
| `buff/cache` | Used for disk caching (can be freed) |
| `available` | RAM available for new programs |
| `Swap` | Disk space used as overflow RAM |

> 💡 `available` is the real number to watch — not `free`. Linux uses RAM for caching, which is normal and efficient.

---

### `vmstat` — Virtual Memory Statistics

```bash
vmstat
vmstat 2        # Update every 2 seconds
vmstat 2 5      # Update every 2 seconds, 5 times
```

---

### `iostat` — CPU and Disk I/O Statistics

```bash
iostat
iostat -x       # Extended statistics
```

---

### `uptime` — How Long the System Has Been Running

```bash
uptime
```

**Output:**
```
10:30:01 up 3:25,  2 users,  load average: 0.15, 0.10, 0.08
```

### Load Average Explained

```
load average: 0.15, 0.10, 0.08
               │      │     │
               │      │     └── 15-minute average
               │      └──────── 5-minute average
               └─────────────── 1-minute average
```

- **< 1.0** per CPU core → system is not overloaded
- **= 1.0** per CPU core → exactly at capacity
- **> 1.0** per CPU core → overloaded, processes are waiting

Check how many CPU cores you have:
```bash
nproc                   # Number of CPU cores
```

---

## 9. System Information Commands

### `uname` — Kernel Information

```bash
uname -a            # All system info
uname -r            # Kernel version only
uname -m            # CPU architecture (x86_64, arm, etc.)
```

---

### `lscpu` — CPU Details

```bash
lscpu
```

**Output includes:**
```
Architecture:    x86_64
CPU(s):          8
Thread(s) per core: 2
Core(s) per socket: 4
CPU MHz:         2400.000
Cache L3:        8192K
```

---

### `lsmem` — Memory Information

```bash
lsmem               # Show memory blocks
```

---

### `lsblk` — Block Devices (Disks)

```bash
lsblk
```

**Output:**
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0   256G  0 disk
├─sda1   8:1    0    50G  0 part /
├─sda2   8:2    0   200G  0 part /home
└─sda3   8:3    0     6G  0 part [SWAP]
```

---

### `lshw` — Full Hardware Info

```bash
sudo lshw                   # Full hardware list
sudo lshw -short            # Short summary
sudo lshw -class memory     # RAM info
sudo lshw -class processor  # CPU info
sudo lshw -class disk       # Disk info
```

---

### `hostname` — Machine Name

```bash
hostname                    # Print machine name
hostname -I                 # Print IP address(es)
```

---

### `/proc` — The Virtual System Info Filesystem

Linux exposes system info as files in `/proc`:

```bash
cat /proc/cpuinfo           # Detailed CPU info
cat /proc/meminfo           # Detailed memory info
cat /proc/version           # Kernel version
cat /proc/uptime            # Uptime in seconds
cat /proc/loadavg           # Load average
ls /proc/                   # Each PID is a directory!
cat /proc/1/status          # Info about process 1 (systemd)
```

---

## 10. Package Management — apt

`apt` (Advanced Package Tool) is the package manager for **Debian and Ubuntu** based systems. It installs, updates, and removes software.

### How apt Works

```
You run: sudo apt install htop
apt checks: package repositories (online software lists)
apt downloads: htop package from the server
apt installs: htop on your system
```

---

### Update Package Lists

Always run this **before installing anything**:

```bash
sudo apt update
```

**Output:**
```
Hit:1 http://archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://security.ubuntu.com/ubuntu focal-security InRelease
...
Fetched 1,234 kB in 3s
Reading package lists... Done
```

> `apt update` only refreshes the **list of available packages** — it does NOT upgrade your software yet.

---

### Upgrade Installed Packages

```bash
sudo apt upgrade            # Upgrade all packages
sudo apt full-upgrade       # Upgrade + remove old dependencies
```

---

### Install a Package

```bash
sudo apt install htop               # Install htop
sudo apt install git curl wget      # Install multiple packages
sudo apt install -y htop            # Auto-confirm (no prompt)
```

---

### Remove a Package

```bash
sudo apt remove htop                # Remove but keep config files
sudo apt purge htop                 # Remove + delete config files
sudo apt autoremove                 # Remove unused dependencies
```

---

### Search for a Package

```bash
apt search htop                     # Search by name/description
apt search "text editor"            # Search by description
```

---

### Get Package Information

```bash
apt show htop                       # Detailed info about a package
apt list --installed                # List all installed packages
apt list --installed | grep python  # Find installed Python packages
apt list --upgradable               # Packages with available updates
```

---

### Full Update Workflow

```bash
sudo apt update               # Step 1: Refresh package list
sudo apt upgrade              # Step 2: Upgrade installed packages
sudo apt autoremove           # Step 3: Clean up unused packages
sudo apt clean                # Step 4: Clear download cache
```

---

### `dpkg` — Low-Level Package Tool

```bash
dpkg -l                         # List all installed packages
dpkg -l | grep python           # Find Python packages
dpkg -s htop                    # Show package status
sudo dpkg -i package.deb        # Install a .deb file manually
sudo dpkg -r htop               # Remove a package
```

---

## 11. Package Management — Other Distros

### Red Hat / Fedora / CentOS — `dnf` / `yum`

```bash
sudo dnf update                     # Update all packages
sudo dnf install htop               # Install
sudo dnf remove htop                # Remove
dnf search htop                     # Search
dnf info htop                       # Package info
```

### Arch Linux — `pacman`

```bash
sudo pacman -Syu                    # Update system
sudo pacman -S htop                 # Install
sudo pacman -R htop                 # Remove
sudo pacman -Rs htop                # Remove + dependencies
pacman -Ss htop                     # Search
pacman -Qi htop                     # Package info
```

### Universal — `snap`

```bash
sudo snap install code              # Install VS Code via snap
sudo snap list                      # List installed snaps
sudo snap remove code               # Remove
sudo snap refresh                   # Update all snaps
```

### Universal — `flatpak`

```bash
flatpak install flathub org.gimp.GIMP   # Install GIMP
flatpak list                             # List installed
flatpak uninstall org.gimp.GIMP         # Remove
flatpak update                          # Update all
```

---

## 12. systemd — Managing Services

**Services** (also called daemons) are background processes that run continuously — like a web server, SSH server, or database.

`systemd` is the system that manages all services on modern Linux.

### `systemctl` — Control Services

```bash
# Check status of a service
sudo systemctl status ssh

# Start a service
sudo systemctl start ssh

# Stop a service
sudo systemctl stop ssh

# Restart a service
sudo systemctl restart ssh

# Reload config without restarting
sudo systemctl reload ssh

# Enable service to start at boot
sudo systemctl enable ssh

# Disable service from starting at boot
sudo systemctl disable ssh

# Check if a service is enabled
sudo systemctl is-enabled ssh

# List all running services
systemctl list-units --type=service --state=running

# List all services (including stopped)
systemctl list-units --type=service
```

---

### `journalctl` — View System Logs

```bash
journalctl                          # All system logs
journalctl -b                       # Logs since last boot
journalctl -f                       # Follow logs in real time (like tail -f)
journalctl -u ssh                   # Logs for SSH service only
journalctl -n 50                    # Last 50 log entries
journalctl --since "1 hour ago"     # Logs from last hour
journalctl --since "2026-05-01"     # Logs from a specific date
```

---

## ✅ Daily Tasks Checklist

- [ ] Run `ps aux` and find your bash process
- [ ] Run `ps aux --sort=-%cpu | head -5` to see top CPU processes
- [ ] Run `top` and navigate with `P`, `M`, `q`
- [ ] Install `htop` with `sudo apt install htop` and explore it
- [ ] Run a command in background: `sleep 60 &`
- [ ] List background jobs with `jobs`
- [ ] Pause a process with `Ctrl+Z` and resume with `bg`
- [ ] Find a process PID using `pgrep bash`
- [ ] Kill a background sleep job with `kill %1`
- [ ] Check disk space with `df -h`
- [ ] Check folder sizes with `du -sh ~`
- [ ] Check memory usage with `free -h`
- [ ] Run `sudo apt update` to refresh package lists
- [ ] Install a package: `sudo apt install tree`
- [ ] Run `tree ~` to see your home folder as a tree
- [ ] Run `systemctl status ssh` to check SSH service

---

## 🏋️ Practice Exercises

### Exercise 1 — Process Investigation

```bash
# See all your running processes
ps -u $(whoami)

# Find the PID of your bash shell
pgrep bash

# Get detailed info about a process
cat /proc/$(pgrep bash | head -1)/status

# See how long the system has been up
uptime

# See the full process tree
pstree -p | head -20
```

---

### Exercise 2 — Job Control Practice

```bash
# Start a long sleep in the background
sleep 200 &
sleep 300 &
sleep 400 &

# List all background jobs
jobs

# Bring job 2 to foreground
fg %2

# Suspend it
# Press Ctrl+Z

# Check jobs again
jobs

# Kill job 1
kill %1

# Check remaining jobs
jobs

# Kill all remaining sleep processes
killall sleep

# Confirm they're gone
jobs
```

---

### Exercise 3 — System Resource Snapshot

```bash
# Create a complete system snapshot
echo "====== SYSTEM SNAPSHOT ======" > system_snapshot.txt
echo "Date: $(date)" >> system_snapshot.txt
echo "" >> system_snapshot.txt

echo "--- UPTIME ---" >> system_snapshot.txt
uptime >> system_snapshot.txt
echo "" >> system_snapshot.txt

echo "--- CPU INFO ---" >> system_snapshot.txt
lscpu | grep -E "Architecture|CPU\(s\)|MHz" >> system_snapshot.txt
echo "" >> system_snapshot.txt

echo "--- MEMORY ---" >> system_snapshot.txt
free -h >> system_snapshot.txt
echo "" >> system_snapshot.txt

echo "--- DISK USAGE ---" >> system_snapshot.txt
df -h >> system_snapshot.txt
echo "" >> system_snapshot.txt

echo "--- TOP 5 PROCESSES BY CPU ---" >> system_snapshot.txt
ps aux --sort=-%cpu | head -6 >> system_snapshot.txt

# View the snapshot
cat system_snapshot.txt
```

---

### Exercise 4 — Package Management Practice

```bash
# Update package list
sudo apt update

# Search for the 'tree' package
apt search tree

# Get info about 'tree'
apt show tree

# Install tree
sudo apt install tree -y

# Use tree to visualize your home directory
tree ~ -L 2

# Check it's installed
dpkg -s tree | grep Status

# Remove it
sudo apt remove tree -y

# Confirm it's gone
which tree    # Should return nothing
```

---

### Exercise 5 — Monitor in Real Time

```bash
# In terminal 1: Start a CPU-intensive loop
for i in $(seq 1 1000000); do echo $i > /dev/null; done &

# Check its PID
pgrep -l bash

# Watch it in top (sort by CPU with P)
top

# Or in htop
htop

# Kill the background loop when done
kill $(pgrep -n bash)
```

---

## 📝 Summary

Today you learned:

| Command | What It Does |
|---------|-------------|
| `ps aux` | List all running processes |
| `ps aux --sort=-%cpu` | Sort processes by CPU usage |
| `top` | Live process monitor |
| `htop` | Interactive process viewer |
| `pgrep name` | Get PID by process name |
| `pstree` | View process tree |
| `kill PID` | Send SIGTERM to a process |
| `kill -9 PID` | Force kill a process |
| `killall name` | Kill all processes by name |
| `pkill pattern` | Kill processes by pattern |
| `sleep 60 &` | Run command in background |
| `jobs` | List background jobs |
| `fg %1` | Bring job 1 to foreground |
| `bg %1` | Resume job 1 in background |
| `Ctrl+Z` | Suspend current process |
| `Ctrl+C` | Stop current process |
| `df -h` | Disk space usage |
| `du -sh folder/` | Folder size summary |
| `free -h` | RAM usage |
| `uptime` | System uptime and load |
| `lscpu` | CPU information |
| `lsblk` | List disk devices |
| `uname -a` | Kernel and system info |
| `sudo apt update` | Refresh package lists |
| `sudo apt upgrade` | Upgrade all packages |
| `sudo apt install pkg` | Install a package |
| `sudo apt remove pkg` | Remove a package |
| `apt search keyword` | Search for packages |
| `systemctl status svc` | Check a service status |
| `systemctl start svc` | Start a service |
| `journalctl -f` | Follow system logs live |

### The Process Lifecycle

```
Create → Running → Sleeping → Stopped → Zombie → Dead
  fork     exec    waiting    Ctrl+Z    exited   reaped
           ↑                    ↓
        start                  bg/fg
```

---

## 🔜 What's Next — Day 6

Tomorrow you will learn **Networking & SSH** — how to check your network, connect to remote machines, and transfer files securely.

Preview of Day 6 commands:

```bash
ip a            # Show network interfaces and IP addresses
ping            # Test network connectivity
ssh             # Secure shell — connect to remote servers
scp             # Copy files over SSH
wget / curl     # Download files from the internet
```

---

> 💬 **Stuck?** Run `man ps`, `man kill`, or `man apt` for full documentation.
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**Day 5 Complete! 🎉**

⭐ You can now monitor, control processes, and manage software like a sysadmin!

[← Day 4](./Day4_Users_and_Permissions.md) | [Back to Learning Plan](./LEARNING_PLAN.md) | [Day 6 →](./Day6_Networking_and_SSH.md)

</div>
