# 🐧 Day 1 — Introduction & Setup

> **Goal:** Understand what Linux is, install it, open the terminal, and run your very first commands.

![Day](https://img.shields.io/badge/Day-1%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Beginner-green?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [What is Linux?](#1-what-is-linux)
2. [Linux vs Windows vs macOS](#2-linux-vs-windows-vs-macos)
3. [Linux Distributions](#3-linux-distributions)
4. [How to Install Linux](#4-how-to-install-linux)
5. [The Terminal — Your New Best Friend](#5-the-terminal--your-new-best-friend)
6. [Your First Commands](#6-your-first-commands)
7. [The Linux File System (Intro)](#7-the-linux-file-system-intro)
8. [Daily Tasks Checklist](#-daily-tasks-checklist)
9. [Practice Exercises](#-practice-exercises)
10. [Summary](#-summary)

---

## 1. What is Linux?

**Linux** is a free and open-source operating system kernel. An operating system (OS) is the software that manages your computer's hardware and allows you to run programs.

Think of it like this:

```
Your Programs (browser, editor, games)
        ↓
  Operating System  ← Linux lives here
        ↓
   Hardware (CPU, RAM, Disk)
```

### Key Facts about Linux

- Created by **Linus Torvalds** in **1991** as a hobby project
- Released under the **GNU General Public License (GPL)** — free for everyone
- The **kernel** is the core part; it talks directly to hardware
- Used to build complete operating systems called **distributions**

### Where is Linux Used?

Linux is everywhere — you've already used it without knowing:

| Where | Example |
|-------|---------|
| 🌐 Web Servers | Over 90% of websites run on Linux |
| 📱 Smartphones | Android is built on the Linux kernel |
| ☁️ Cloud | AWS, Google Cloud, Azure all run Linux |
| 🖥️ Supercomputers | 100% of the world's top 500 supercomputers |
| 📺 Smart TVs | Most run embedded Linux |
| 🚗 Cars | Tesla, BMW use Linux in their systems |

---

## 2. Linux vs Windows vs macOS

| Feature | Linux | Windows | macOS |
|---------|-------|---------|-------|
| **Cost** | Free | Paid (~$139) | Free (needs Apple hardware) |
| **Open Source** | ✅ Yes | ❌ No | ❌ No |
| **Customizable** | ✅ Fully | ⚠️ Limited | ⚠️ Limited |
| **Security** | ✅ Very secure | ⚠️ More vulnerable | ✅ Secure |
| **Virus Risk** | Very low | High | Low |
| **Performance** | ✅ Lightweight | ⚠️ Heavy | ✅ Good |
| **Gaming** | ⚠️ Improving | ✅ Best | ⚠️ Limited |
| **Used by Developers** | ✅ Most popular | ⚠️ Common | ✅ Popular |
| **Terminal Power** | ✅ Excellent | ⚠️ Limited (PowerShell) | ✅ Good (Unix-based) |

### Why Should You Learn Linux?

- Most **servers and cloud infrastructure** run Linux
- **Developers and engineers** are expected to know it
- It is **fast, stable, and secure**
- It teaches you how computers and operating systems truly work
- It is **completely free** — no licenses needed

---

## 3. Linux Distributions

A **Linux distribution (distro)** is a complete operating system built around the Linux kernel. Different distros are designed for different use cases.

```
Linux Kernel (the core)
        │
        ├── Ubuntu          → Beginners, general use
        ├── Debian          → Servers, stability
        ├── Fedora          → Developers
        ├── Arch Linux      → Advanced, customizable
        ├── Kali Linux      → Cybersecurity
        ├── Linux Mint      → Windows switchers
        ├── CentOS / RHEL   → Enterprise servers
        └── Raspberry Pi OS → Embedded / IoT
```

### Which Distro Should You Choose as a Beginner?

#### ✅ Ubuntu — Recommended for Beginners

- Most popular desktop Linux distro
- Huge community and support online
- Regular updates and long-term support (LTS) versions
- Works on most hardware out of the box

```
Download: https://ubuntu.com/download
```

#### ✅ Linux Mint — Great for Windows Switchers

- Familiar desktop layout (similar to Windows)
- Very stable and beginner-friendly
- Comes with media codecs pre-installed

#### ✅ WSL2 (Windows Subsystem for Linux) — If You Want to Stay on Windows

- Run Linux inside Windows without dual booting
- Perfect for learning the terminal without switching OS
- Install from the Microsoft Store

---

## 4. How to Install Linux

There are several ways to get Linux running. Choose the one that suits you:

---

### Option A — WSL2 on Windows (Easiest for Beginners)

WSL2 lets you run Linux inside Windows. No need to partition your disk.

**Step 1:** Open PowerShell as Administrator

```powershell
wsl --install
```

**Step 2:** Restart your computer

**Step 3:** Open "Ubuntu" from the Start Menu

**Step 4:** Create your username and password

```
Enter new UNIX username: yourname
New password: ••••••••
```

Done! You now have a Linux terminal inside Windows.

---

### Option B — Virtual Machine (Safe and Isolated)

Run Linux inside your current OS using VirtualBox — no risk to your system.

**Step 1:** Download VirtualBox
```
https://www.virtualbox.org/
```

**Step 2:** Download Ubuntu ISO
```
https://ubuntu.com/download/desktop
```

**Step 3:** Open VirtualBox → New → Name it "Ubuntu" → Select the ISO

**Step 4:** Set RAM to at least 2048 MB and disk to 20 GB

**Step 5:** Start the VM and follow the Ubuntu installer

---

### Option C — Live USB (Try Before Installing)

Boot Linux from a USB drive — nothing is installed on your computer.

**Step 1:** Download Ubuntu ISO

**Step 2:** Download Balena Etcher
```
https://www.balena.io/etcher/
```

**Step 3:** Flash the ISO to a USB drive (8 GB minimum)

**Step 4:** Restart your computer and boot from USB (press F12 or F2)

**Step 5:** Choose "Try Ubuntu" — you're live!

---

### Option D — Dual Boot (Full Power, Permanent)

Install Linux alongside Windows on the same machine.

> ⚠️ **Warning:** Back up your data before dual booting. Partitioning your disk incorrectly can cause data loss.

**General Steps:**
1. Create a bootable USB (same as Option C)
2. Boot from USB
3. Choose "Install Ubuntu alongside Windows"
4. Follow the guided installer
5. Restart — choose your OS at the boot menu

---

## 5. The Terminal — Your New Best Friend

The **terminal** (also called the command line, shell, or console) is a text-based interface to control your computer. Instead of clicking icons, you type commands.

### Why Use the Terminal?

- Much **faster** than clicking through menus
- Allows **automation** — do in one line what takes 10 clicks
- Essential for **servers** (no desktop GUI)
- Gives you **full control** over the system

### Opening the Terminal

| System | How to Open |
|--------|-------------|
| Ubuntu Desktop | Press `Ctrl + Alt + T` |
| Ubuntu Desktop | Right-click desktop → "Open Terminal" |
| WSL2 on Windows | Search "Ubuntu" in Start Menu |
| macOS (for comparison) | `Cmd + Space` → type "Terminal" |

### What You See (The Prompt)

When the terminal opens, you see something like:

```bash
yourname@hostname:~$
```

Let's break this down:

```
yourname   →  your username
@          →  "at"
hostname   →  your computer's name
:          →  separator
~          →  current directory (~ means home folder)
$          →  you are a regular user (# means root/admin)
```

### The Shell

The **shell** is the program that reads your commands and executes them. The most common shell is **Bash** (Bourne Again SHell).

```bash
# Check which shell you are using
echo $SHELL

# Output
/bin/bash
```

---

## 6. Your First Commands

Let's learn the most essential beginner commands one by one.

---

### `pwd` — Print Working Directory

Shows you **where you currently are** in the file system.

```bash
pwd
```

**Output:**
```
/home/yourname
```

This means you are inside the folder `/home/yourname` — your home directory.

---

### `ls` — List Directory Contents

Shows **what files and folders** are in your current location.

```bash
ls
```

**Output:**
```
Desktop  Documents  Downloads  Music  Pictures  Videos
```

#### Useful `ls` options:

```bash
ls -l       # Long format — shows permissions, size, date
ls -a       # Show hidden files (files starting with .)
ls -la      # Combine both — long format + hidden files
ls -lh      # Human-readable file sizes (KB, MB, GB)
```

**Example output of `ls -la`:**
```
total 48
drwxr-xr-x  8 user user 4096 May 14 09:00 .
drwxr-xr-x 20 root root 4096 May 13 10:00 ..
-rw-r--r--  1 user user  220 May 13 10:00 .bash_logout
-rw-r--r--  1 user user 3526 May 13 10:00 .bashrc
drwxr-xr-x  2 user user 4096 May 14 09:00 Desktop
drwxr-xr-x  2 user user 4096 May 14 09:00 Documents
```

---

### `cd` — Change Directory

Moves you **into a different folder**.

```bash
cd Documents        # Go into the Documents folder
cd ..               # Go up one level (parent folder)
cd ~                # Go to your home directory
cd /                # Go to the root of the file system
cd /home/yourname   # Go to a specific path (absolute path)
```

#### Absolute vs Relative Paths

```bash
# Absolute path — starts from root /
cd /home/yourname/Documents

# Relative path — starts from where you currently are
cd Documents        # if you are already in /home/yourname
```

#### Handy Shortcuts

```bash
cd ~        # Home directory
cd -        # Previous directory (go back)
cd ..       # One level up
cd ../..    # Two levels up
```

---

### `whoami` — Who Are You?

Prints your **current username**.

```bash
whoami
```

**Output:**
```
yourname
```

---

### `echo` — Print Text

Prints any text or variable to the terminal.

```bash
echo "Hello, Linux!"
echo $HOME          # Print the value of the HOME variable
echo $USER          # Print current username
```

**Output:**
```
Hello, Linux!
/home/yourname
yourname
```

---

### `clear` — Clear the Terminal

Clears all previous output so you have a clean screen.

```bash
clear
```

> 💡 Shortcut: `Ctrl + L` does the same thing

---

### `uname` — System Information

Shows information about your Linux system.

```bash
uname           # Print OS name
uname -a        # Print all system info (kernel, hostname, arch)
```

**Output of `uname -a`:**
```
Linux hostname 5.15.0-91-generic #101-Ubuntu SMP x86_64 GNU/Linux
```

---

### `date` — Current Date and Time

```bash
date
```

**Output:**
```
Thu May 14 09:30:00 UTC 2026
```

---

### `man` — Manual Pages (Built-in Help)

Shows the full manual for any command. This is one of the most important commands to know.

```bash
man ls          # Manual for ls
man cd          # Manual for cd
man pwd         # Manual for pwd
```

Inside `man`, use:
- `Arrow keys` or `j/k` to scroll
- `q` to quit
- `/word` to search for a word

> 💡 If `man` is too detailed, try:
> ```bash
> ls --help       # Quick summary of options
> ```

---

## 7. The Linux File System (Intro)

Linux organizes everything in a **single tree** starting from the root `/`. Unlike Windows (C:\, D:\), there is only one root.

```
/                        ← Root (top of everything)
├── home/                ← User home directories
│   └── yourname/        ← YOUR home folder (~)
│       ├── Desktop/
│       ├── Documents/
│       └── Downloads/
├── etc/                 ← System configuration files
├── bin/                 ← Essential programs (ls, cp, mv)
├── usr/                 ← User programs and software
├── var/                 ← Logs, databases, variable data
├── tmp/                 ← Temporary files (cleared on reboot)
└── root/                ← Home folder for the root (admin) user
```

### Your Home Directory

Your personal space is `/home/yourname` — represented as `~` in the terminal.

```bash
echo ~          # Shows /home/yourname
cd ~            # Takes you there
pwd             # Confirms: /home/yourname
```

Everything you create as a regular user should live inside your home directory. You do **not** need admin permission there.

---

## ✅ Daily Tasks Checklist

Complete these tasks to finish Day 1:

- [ ] Install Ubuntu, Linux Mint, or set up WSL2 on Windows
- [ ] Open the terminal successfully
- [ ] Run `pwd` and understand the output
- [ ] Run `ls` and see your home folder contents
- [ ] Run `ls -la` and view the long format output
- [ ] Navigate into a folder using `cd Documents`
- [ ] Go back to home using `cd ~`
- [ ] Run `whoami` and see your username
- [ ] Run `uname -a` and see your system info
- [ ] Use `man ls` to read the manual for `ls` (press `q` to quit)
- [ ] Clear the terminal with `clear` or `Ctrl + L`

---

## 🏋️ Practice Exercises

Try these on your own to reinforce what you learned:

### Exercise 1 — Navigate the File System

```bash
# Start from home
cd ~

# Go into Documents
cd Documents

# Check where you are
pwd

# Go up one level
cd ..

# Check again
pwd

# Go back to where you were
cd -
```

### Exercise 2 — Explore the Root

```bash
# Go to root
cd /

# List what's there
ls

# Check the /home folder
ls /home

# Return to your home
cd ~
```

### Exercise 3 — Use `echo` and Variables

```bash
echo "My name is $(whoami)"
echo "I am in the directory: $(pwd)"
echo "My home folder is: $HOME"
echo "Today is: $(date)"
```

**Expected output (example):**
```
My name is yourname
I am in the directory: /home/yourname
My home folder is: /home/yourname
Today is: Thu May 14 09:30:00 UTC 2026
```

### Exercise 4 — Read a Manual

```bash
man pwd          # Read the manual for pwd
                 # Press q to exit
pwd --help       # Shorter help summary
```

---

## 📝 Summary

Today you learned:

| Concept | What You Learned |
|---------|-----------------|
| **Linux** | Free, open-source OS kernel used everywhere |
| **Distributions** | Ubuntu, Mint, Fedora — full OS built on the kernel |
| **Installation** | WSL2, VirtualBox, Live USB, or Dual Boot |
| **Terminal** | Text interface to control Linux with commands |
| **Shell / Bash** | The program that reads and runs your commands |
| **`pwd`** | Shows your current directory |
| **`ls`** | Lists files and folders |
| **`cd`** | Navigates between directories |
| **`whoami`** | Shows your username |
| **`echo`** | Prints text or variables |
| **`man`** | Opens the manual for any command |
| **File System** | Everything starts at root `/`, home is `~` |

---

## 🔜 What's Next — Day 2

Tomorrow you will learn how to **create, copy, move, and delete files and directories** — the building blocks of working with any Linux system.

Preview of Day 2 commands:

```bash
mkdir    # Create a directory
touch    # Create a file
cp       # Copy files
mv       # Move or rename files
rm       # Delete files
cat      # View file contents
```

---

> 💬 **Stuck?** Run `man <command>` or type `<command> --help` for instant guidance.
> 
> 🌐 **Community help:** https://unix.stackexchange.com

---

<div align="center">

**Day 1 Complete! 🎉**

⭐ You just took your first step into the Linux world!

[← Back to Learning Plan](./LEARNING_PLAN.md)

</div>
