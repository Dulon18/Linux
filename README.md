# 🐧 7-Day Linux Learning Plan

> A structured, beginner-friendly roadmap to go from zero to confident Linux terminal user — one topic per day.

![Linux](https://img.shields.io/badge/Linux-Learning%20Plan-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Duration](https://img.shields.io/badge/Duration-7%20Days-blue?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Beginner%20to%20Intermediate-green?style=for-the-badge)

---

## 📋 Overview

| Day | Topic | Focus Area |
|-----|-------|------------|
| Day 1 | Introduction & Setup | Install Linux, meet the terminal |
| Day 2 | Files & Directories | Create, move, copy, delete files |
| Day 3 | Text & Search | grep, find, pipes, redirection |
| Day 4 | Users & Permissions | chmod, chown, sudo |
| Day 5 | Processes & System | Monitor system, manage packages |
| Day 6 | Networking & SSH | Connect, transfer files, go remote |
| Day 7 | Shell Scripting | Automate tasks with bash |

> ⏱️ **Time commitment:** 1–2 hours per day

---

## 📅 Day 1 — Introduction & Setup

**Goal:** Install Linux and run your first terminal commands.

### Topics Covered
- What is Linux and its distributions
- Install Ubuntu / Linux Mint or set up WSL2 on Windows
- Opening and navigating the terminal
- Basic navigation commands

### ✅ Daily Tasks
- [ ] Install Ubuntu, Linux Mint, or set up WSL2 on Windows
- [ ] Open the terminal for the first time
- [ ] Use `pwd` to find where you are
- [ ] Use `ls` to list directory contents
- [ ] Use `cd` to navigate between directories
- [ ] Read: "What is Linux?" (30 min)

### 💻 Commands to Practice

```bash
pwd              # Print current working directory
ls               # List files and directories
ls -la           # List all files with details
cd ~             # Go to home directory
cd /home         # Navigate to /home directory
whoami           # Show current logged-in user
```

---

## 📅 Day 2 — Files & Directories

**Goal:** Create, copy, move, and delete files and folders confidently.

### Topics Covered
- Creating files and directories
- Copying and moving files
- Deleting files safely
- Viewing file contents

### ✅ Daily Tasks
- [ ] Create a folder called `my-linux-practice`
- [ ] Create 3 text files using `touch`
- [ ] Copy a file using `cp`
- [ ] Move/rename a file using `mv`
- [ ] Delete a file using `rm`
- [ ] View file contents with `cat` and `less`

### 💻 Commands to Practice

```bash
mkdir my-folder          # Create a new directory
touch file.txt           # Create an empty file
cp file.txt copy.txt     # Copy a file
mv file.txt renamed.txt  # Move or rename a file
rm file.txt              # Delete a file
rm -rf folder/           # Delete a folder (use carefully!)
cat file.txt             # Print file contents
less file.txt            # Scroll through a file
```

---

## 📅 Day 3 — Text & Search

**Goal:** Find files, search inside them, and filter output using pipes.

### Topics Covered
- Text viewers: `head`, `tail`, `less`
- `grep` — search inside files
- `find` — locate files on disk
- Pipes (`|`) and output redirection (`>`)

### ✅ Daily Tasks
- [ ] Use `head` and `tail` to view parts of a file
- [ ] Search for a word with `grep`
- [ ] Find a file by name using `find`
- [ ] Pipe output: `ls | grep txt`
- [ ] Redirect output to a file: `ls > list.txt`
- [ ] Count lines in a file with `wc -l`

### 💻 Commands to Practice

```bash
head -n 5 file.txt         # Show first 5 lines
tail -n 10 file.txt        # Show last 10 lines
grep "word" file.txt       # Search for text in file
grep -r "word" ./          # Recursive search in folder
find . -name "*.txt"       # Find all .txt files
ls | grep txt              # Pipe: filter ls output
ls > output.txt            # Redirect output to file
wc -l file.txt             # Count lines in a file
```

---

## 📅 Day 4 — Users & Permissions

**Goal:** Understand Linux file ownership and control who can access what.

### Topics Covered
- Users and groups in Linux
- File permissions: read (r), write (w), execute (x)
- `chmod` and `chown`
- `sudo` — superuser access

### ✅ Daily Tasks
- [ ] Run `whoami` and `id` to see your user info
- [ ] Check file permissions with `ls -la`
- [ ] Make a script executable with `chmod +x`
- [ ] Change file ownership with `chown`
- [ ] Use `sudo` to run a command as root
- [ ] Create a new user with `sudo adduser`

### 📖 Understanding Permissions

```
-rwxr-xr--  1  user  group  size  date  filename
 |||||||
 ||||||└── Others: read (r)
 |||||└─── Others: no write (-)
 ||||└──── Others: no execute (-)
 |||└───── Group: read (r)
 ||└────── Group: no write (-)
 |└─────── Group: execute (x)
 └──────── Owner: read (r) + write (w) + execute (x)
```

### 💻 Commands to Practice

```bash
whoami                    # Show current user
id                        # Show user and group IDs
ls -la                    # View file permissions
chmod 755 script.sh       # rwxr-xr-x
chmod +x script.sh        # Add execute permission
sudo chown user file      # Change file owner
sudo adduser newuser      # Create a new user
```

---

## 📅 Day 5 — Processes & System

**Goal:** Monitor what's running on your system and manage software packages.

### Topics Covered
- Viewing running processes
- Killing processes
- Disk and memory usage
- Installing packages with `apt`

### ✅ Daily Tasks
- [ ] List processes with `ps aux`
- [ ] Use `top` or `htop` to monitor in real time
- [ ] Kill a process by its PID
- [ ] Check disk space with `df -h`
- [ ] Check memory usage with `free -h`
- [ ] Install a package: `sudo apt install htop`

### 💻 Commands to Practice

```bash
ps aux                    # List all running processes
top                       # Real-time process monitor
htop                      # Interactive process viewer
kill 1234                 # Kill process with PID 1234
kill -9 1234              # Force kill a process
df -h                     # Disk space usage
free -h                   # Memory usage
sudo apt update           # Update package list
sudo apt install htop     # Install a package
sudo apt remove htop      # Remove a package
```

---

## 📅 Day 6 — Networking & SSH

**Goal:** Understand networking basics and connect to remote servers securely.

### Topics Covered
- Checking network info with `ip` and `ping`
- Downloading files with `curl` and `wget`
- SSH — connecting to remote machines
- Copying files over SCP

### ✅ Daily Tasks
- [ ] Run `ip a` to see your IP address
- [ ] Ping a website: `ping google.com`
- [ ] Download a file using `wget` or `curl`
- [ ] Generate an SSH key pair
- [ ] Connect to a remote machine via SSH
- [ ] Copy a file to a server using `scp`

### 💻 Commands to Practice

```bash
ip a                              # Show IP addresses
ping -c 4 google.com              # Test connectivity (4 packets)
curl https://example.com          # HTTP request
wget https://example.com/file     # Download a file
ss -tulpn                         # Show open ports

# SSH key setup
ssh-keygen -t ed25519 -C "your@email.com"  # Generate key pair
ssh-copy-id user@remote_host               # Copy key to server
ssh user@remote_host                       # Connect without password

# Transfer files
scp file.txt user@host:/path/to/dest       # Copy file to server
scp user@host:/path/file.txt ./            # Copy file from server
```

---

## 📅 Day 7 — Shell Scripting

**Goal:** Write your first bash scripts to automate repetitive tasks.

### Topics Covered
- Variables and user input
- If/else conditions
- Loops: `for` and `while`
- Functions

### ✅ Daily Tasks
- [ ] Write your first bash script with `#!/bin/bash`
- [ ] Use variables and `echo` to print output
- [ ] Write an if/else condition
- [ ] Write a for loop
- [ ] Define and call a function
- [ ] Make the script executable and run it

### 💻 Your First Bash Script

```bash
#!/bin/bash
# my_first_script.sh

# Variables
NAME="Linux Learner"
VERSION=7

echo "Welcome, $NAME!"
echo "You have completed Day $VERSION of 7."

# Conditionals
if [ $VERSION -eq 7 ]; then
    echo "Congratulations! You finished the 7-day plan!"
else
    echo "Keep going, you are on day $VERSION!"
fi

# For loop
echo "Days completed:"
for i in 1 2 3 4 5 6 7; do
    echo "  ✓ Day $i"
done

# Function
greet() {
    echo "Hello, $1! You are a Linux user now."
}

greet "$NAME"
```

```bash
# Run your script
chmod +x my_first_script.sh
./my_first_script.sh
```

---

## 🎯 After 7 Days — What's Next?

Once you complete this plan, you're ready to explore:

- **Vim / Nano** — terminal-based text editors
- **Cron Jobs** — schedule automated tasks
- **systemd** — manage system services
- **Docker** — containerize applications
- **Git on Linux** — version control from the terminal
- **Networking deep dive** — firewalls, iptables, DNS
- **Linux server administration** — web servers, databases

---

## 📚 Recommended Resources

| Resource | Type | Link |
|----------|------|-------|
| The Linux Command Line | Free book | https://linuxcommand.org/tlcl.php |
| Linux Journey | Interactive tutorials | https://linuxjourney.com |
| OverTheWire: Bandit | Learn by playing | https://overthewire.org/wargames/bandit |
| TryHackMe Linux Fundamentals | Hands-on labs | https://tryhackme.com |
| r/linuxquestions | Community Q&A | https://reddit.com/r/linuxquestions |

---

## 💡 Tips for Success

- **Practice every day** — even 30 minutes is enough to build muscle memory
- **Use `man <command>`** — the built-in manual is your best friend
- **Don't fear the terminal** — mistakes are reversible (mostly!)
- **Break things in a VM** — safe sandbox for experimentation
- **Keep a personal cheat sheet** — write down commands you learn daily

---

## 📄 License

This learning plan is open and free to use, share, and modify.

---

<div align="center">

Happy learning!

⭐ Star this repo if this plan helped you!

</div>
