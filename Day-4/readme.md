# 🐧 Day 4 — Users & Permissions

> **Goal:** Understand Linux user management, file ownership, and permission system — control who can read, write, and execute files.

![Day](https://img.shields.io/badge/Day-4%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Beginner--Intermediate-orange?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 3](#1-quick-recap-of-day-3)
2. [Linux User System — The Big Picture](#2-linux-user-system--the-big-picture)
3. [User Info Commands](#3-user-info-commands)
4. [Understanding File Permissions](#4-understanding-file-permissions)
5. [chmod — Change File Permissions](#5-chmod--change-file-permissions)
6. [chown — Change File Ownership](#6-chown--change-file-ownership)
7. [chgrp — Change Group Ownership](#7-chgrp--change-group-ownership)
8. [sudo — Superuser Power](#8-sudo--superuser-power)
9. [User Management](#9-user-management)
10. [Group Management](#10-group-management)
11. [The `/etc/passwd` and `/etc/shadow` Files](#11-the-etcpasswd-and-etcshadow-files)
12. [Special Permissions — setuid, setgid, sticky bit](#12-special-permissions--setuid-setgid-sticky-bit)
13. [umask — Default Permission Mask](#13-umask--default-permission-mask)
14. [Daily Tasks Checklist](#-daily-tasks-checklist)
15. [Practice Exercises](#-practice-exercises)
16. [Summary](#-summary)

---

## 1. Quick Recap of Day 3

Make sure you remember these from Day 3:

```bash
grep "word" file.txt       # Search inside a file
grep -r "word" ./          # Recursive search
find . -name "*.txt"       # Find files by name
find . -type f -size +1M   # Find large files
cmd1 | cmd2                # Pipe output to another command
cmd > file.txt             # Redirect output to file
wc -l file.txt             # Count lines
sort file.txt              # Sort alphabetically
```

Today we go into **who owns files and who can do what with them** — a critical concept for security in Linux.

---

## 2. Linux User System — The Big Picture

Linux is a **multi-user operating system**. Multiple users can log in and use the system at the same time, each with their own files and permissions.

### Three Types of Users

```
┌─────────────────────────────────────────────────────┐
│                   Linux Users                       │
│                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────┐ │
│  │  Root User   │  │ Regular User │  │  Service  │ │
│  │  (UID = 0)   │  │ (UID ≥ 1000) │  │  Users    │ │
│  │              │  │              │  │(UID 1-999)│ │
│  │ Full control │  │ Limited      │  │ www-data  │ │
│  │ of system    │  │ to own files │  │ mysql etc │ │
│  └──────────────┘  └──────────────┘  └───────────┘ │
└─────────────────────────────────────────────────────┘
```

| User Type | UID Range | Description |
|-----------|-----------|-------------|
| **Root** | 0 | Superuser — can do anything on the system |
| **System/Service** | 1 – 999 | Created by software (nginx, mysql, etc.) |
| **Regular Users** | 1000+ | Normal human users |

### Every File Has an Owner

Every file in Linux belongs to:
- A **user** (owner)
- A **group** (group owner)

```bash
ls -la fruits.txt
# -rw-r--r-- 1 alice developers 58 May 14 fruits.txt
#              │     │
#              │     └─ Group: developers
#              └─ Owner: alice
```

---

## 3. User Info Commands

### `whoami` — Current Username

```bash
whoami
# Output: alice
```

### `id` — Full User Identity

```bash
id
```

**Output:**
```
uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),1002(developers)
```

Breaking it down:

| Part | Meaning |
|------|---------|
| `uid=1001(alice)` | User ID and username |
| `gid=1001(alice)` | Primary group ID and name |
| `groups=...` | All groups this user belongs to |

```bash
id username          # Check another user's info
id -u                # Print only the UID number
id -g                # Print only the primary GID
id -G                # Print all group IDs
```

### `who` — Who is Logged In Right Now

```bash
who
```

**Output:**
```
alice   pts/0   2026-05-14 09:00 (:0)
bob     pts/1   2026-05-14 09:30 (192.168.1.5)
```

### `w` — Who is Logged In + What They're Doing

```bash
w
```

**Output:**
```
 09:45:00 up 2 days,  3:22,  2 users,  load average: 0.10
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
alice    pts/0    :0               09:00    0.00s  0.12s  0.00s bash
bob      pts/1    192.168.1.5      09:30    5:00   0.05s  0.01s vim
```

### `last` — Login History

```bash
last              # Show login history for all users
last alice        # Show login history for alice
last -n 5         # Show last 5 logins
```

### `finger` — Detailed User Info (if installed)

```bash
finger alice
```

---

## 4. Understanding File Permissions

This is one of the most important concepts in Linux. Every file has a **permission string** that looks like this:

```
-rwxr-xr--
```

### Breaking Down the Permission String

```
-  rwx  r-x  r--
│   │    │    │
│   │    │    └─── Others  (everyone else)
│   │    └──────── Group   (group members)
│   └───────────── Owner   (the file's owner)
└───────────────── File type
```

### File Type Character

| Character | Meaning |
|-----------|---------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `b` | Block device (hard drive) |
| `c` | Character device (keyboard) |
| `p` | Named pipe |
| `s` | Socket |

### Permission Characters

Each group (Owner, Group, Others) has 3 characters:

| Character | Position | Meaning |
|-----------|----------|---------|
| `r` | 1st | Read permission |
| `w` | 2nd | Write permission |
| `x` | 3rd | Execute permission |
| `-` | Any | Permission NOT granted |

### What r, w, x Mean for Files vs Directories

| Permission | On a File | On a Directory |
|------------|-----------|----------------|
| `r` (read) | Can view file contents | Can list directory contents (`ls`) |
| `w` (write) | Can modify file contents | Can create/delete files inside |
| `x` (execute) | Can run the file as a program | Can enter the directory (`cd`) |

### Reading Permission Examples

```
-rwxrwxrwx   Owner: rwx   Group: rwx   Others: rwx  (everyone can do everything)
-rw-r--r--   Owner: rw-   Group: r--   Others: r--  (owner can edit, others read-only)
-rwx------   Owner: rwx   Group: ---   Others: ---  (only owner can do anything)
drwxr-xr-x   Owner: rwx   Group: r-x   Others: r-x  (standard directory)
----------   No one has any permission!
```

### Numeric (Octal) Permission Representation

Each permission has a numeric value:

| Permission | Value |
|------------|-------|
| `r` (read) | 4 |
| `w` (write) | 2 |
| `x` (execute) | 1 |
| `-` (none) | 0 |

You **add** the values for each group:

```
rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
-wx = 0+2+1 = 3
-w- = 0+2+0 = 2
--x = 0+0+1 = 1
--- = 0+0+0 = 0
```

So `-rwxr-xr--` = **754**

```
Owner: rwx = 7
Group: r-x = 5
Others: r-- = 4
Result: 754
```

### Common Permission Numbers

| Number | Permission | Meaning |
|--------|-----------|---------|
| `777` | `rwxrwxrwx` | Full access for everyone (dangerous!) |
| `755` | `rwxr-xr-x` | Standard for executables and directories |
| `644` | `rw-r--r--` | Standard for regular files |
| `600` | `rw-------` | Private file (only owner can read/write) |
| `700` | `rwx------` | Private directory |
| `664` | `rw-rw-r--` | Owner and group can edit |
| `400` | `r--------` | Read-only (even for owner) |

---

## 5. chmod — Change File Permissions

`chmod` stands for **change mode**. It changes the permissions of a file or directory.

### Syntax

```bash
chmod [options] permissions filename
```

There are two ways to use `chmod`:
- **Numeric (octal) mode** — using numbers
- **Symbolic mode** — using letters

---

### Numeric Mode

```bash
chmod 755 script.sh        # rwxr-xr-x
chmod 644 document.txt     # rw-r--r--
chmod 600 private.txt      # rw-------
chmod 777 shared.txt       # rwxrwxrwx (be careful!)
chmod 000 locked.txt       # ---------- (no one can access!)
```

---

### Symbolic Mode

Symbolic mode uses:
- **Who:** `u` (user/owner), `g` (group), `o` (others), `a` (all)
- **Action:** `+` (add), `-` (remove), `=` (set exactly)
- **Permission:** `r`, `w`, `x`

```bash
chmod +x script.sh          # Add execute for everyone
chmod u+x script.sh         # Add execute for owner only
chmod g+w file.txt          # Add write for group
chmod o-r private.txt       # Remove read from others
chmod a-x file.txt          # Remove execute from everyone
chmod u=rwx,g=rx,o=r file   # Set exact permissions
chmod go-w file.txt         # Remove write from group and others
```

### Recursive chmod with `-R`

Apply permissions to a folder and all its contents:

```bash
chmod -R 755 projects/       # Apply to folder and everything inside
chmod -R 644 documents/      # Make all docs read-only for others
```

### Practical Examples

```bash
# Make a shell script executable
touch deploy.sh
chmod +x deploy.sh
./deploy.sh                  # Now you can run it!

# Protect a private key file
chmod 600 ~/.ssh/id_rsa      # SSH keys MUST be 600 or SSH refuses to use them

# Set up a public web directory
chmod 755 /var/www/html/

# Make a file completely private
chmod 600 my_passwords.txt
```

---

## 6. chown — Change File Ownership

`chown` changes the **owner** and optionally the **group** of a file.

### Syntax

```bash
chown [new_owner] filename
chown [new_owner]:[new_group] filename
```

### Examples

```bash
sudo chown bob file.txt              # Change owner to bob
sudo chown bob:developers file.txt   # Change owner and group
sudo chown :developers file.txt      # Change group only (note the colon)
sudo chown alice:alice file.txt      # Set both owner and group to alice
```

### Recursive chown with `-R`

```bash
sudo chown -R alice:alice /home/alice/     # Fix ownership of home folder
sudo chown -R www-data:www-data /var/www/  # Web server ownership
```

### Check ownership before and after

```bash
ls -la file.txt
# -rw-r--r-- 1 root root 58 May 14 file.txt

sudo chown alice:alice file.txt

ls -la file.txt
# -rw-r--r-- 1 alice alice 58 May 14 file.txt
```

> 💡 You need `sudo` to change ownership unless you own the file and are giving it to yourself.

---

## 7. chgrp — Change Group Ownership

`chgrp` changes only the **group** of a file (a shortcut instead of using `chown :group`).

```bash
sudo chgrp developers file.txt         # Change group to developers
sudo chgrp -R www-data /var/www/       # Recursive group change
```

---

## 8. sudo — Superuser Power

`sudo` stands for **superuser do**. It lets a regular user run a command with **root (admin) privileges** temporarily.

### Why sudo Exists

Instead of logging in as root all the time (dangerous), `sudo` lets you:
- Run just one command as root
- Leave an audit trail of who ran what
- Reduce the risk of accidental system damage

```
Regular user:  alice
      │
      └──── sudo command ──→ runs as ROOT
                                    │
                            Full system access
                            for this one command
```

### Basic Usage

```bash
sudo command                    # Run a command as root
sudo apt update                 # Update packages (needs root)
sudo apt install nginx          # Install software
sudo systemctl restart nginx    # Restart a service
sudo nano /etc/hosts            # Edit a system file
```

When you run `sudo`, it asks for YOUR password (not root's):

```
[sudo] password for alice: ••••••••
```

### `sudo -i` — Become Root Temporarily

```bash
sudo -i             # Open a root shell (# prompt)
exit                # Return to normal user
```

### `sudo -l` — List What You Can Run with sudo

```bash
sudo -l
```

**Output:**
```
User alice may run the following commands on this host:
    (ALL : ALL) ALL
```

### `su` — Switch User

```bash
su bob              # Switch to user bob (asks for bob's password)
su -                # Switch to root (asks for root's password)
su - alice          # Switch to alice with her environment
exit                # Return to previous user
```

### The sudoers File

`/etc/sudoers` defines who can use `sudo`. **Never edit it directly** — use `visudo`:

```bash
sudo visudo
```

Common sudoers entries:

```
# Allow alice to run everything with sudo
alice ALL=(ALL:ALL) ALL

# Allow bob to run apt without a password
bob ALL=(ALL) NOPASSWD: /usr/bin/apt
```

---

## 9. User Management

### Add a New User

```bash
sudo adduser alice              # Interactive — asks for password, name, etc.
sudo useradd alice              # Basic — no home directory, no password prompt
sudo useradd -m -s /bin/bash alice  # With home dir and bash shell
```

> 💡 Use `adduser` for interactive setup. Use `useradd` for scripting.

### Set or Change a Password

```bash
sudo passwd alice               # Set/change alice's password
passwd                          # Change your OWN password
```

### Modify a User — `usermod`

```bash
sudo usermod -aG sudo alice         # Add alice to sudo group
sudo usermod -aG developers alice   # Add alice to developers group
sudo usermod -s /bin/zsh alice      # Change shell to zsh
sudo usermod -l newname alice       # Rename user alice to newname
sudo usermod -d /new/home alice     # Change home directory
sudo usermod -L alice               # Lock account (disable login)
sudo usermod -U alice               # Unlock account
```

> ⚠️ Always use `-aG` (append to group). Using `-G` alone **removes** the user from all other groups!

### Delete a User

```bash
sudo deluser alice                  # Delete user (keep home directory)
sudo deluser --remove-home alice    # Delete user AND their home folder
sudo userdel alice                  # Basic delete (no home cleanup)
sudo userdel -r alice               # Delete user and home directory
```

### List All Users

```bash
cat /etc/passwd                     # All users on the system
cat /etc/passwd | cut -d: -f1       # Just the usernames
getent passwd                       # Better way to list users
```

---

## 10. Group Management

Groups let you apply the same permissions to multiple users at once.

### Create a Group

```bash
sudo groupadd developers            # Create a new group
sudo groupadd -g 1500 devops        # Create with specific GID
```

### Add User to a Group

```bash
sudo usermod -aG developers alice   # Add alice to developers group
sudo usermod -aG developers bob     # Add bob to developers group
```

> 💡 The user must **log out and back in** for group changes to take effect.

### Check Group Membership

```bash
groups alice                        # List all groups alice belongs to
id alice                            # Full user and group info
cat /etc/group | grep developers    # See who is in the developers group
```

### Remove User from a Group

```bash
sudo gpasswd -d alice developers    # Remove alice from developers group
```

### Delete a Group

```bash
sudo groupdel developers            # Delete the group
```

### List All Groups

```bash
cat /etc/group                      # All groups on the system
cat /etc/group | cut -d: -f1        # Just the group names
```

---

## 11. The `/etc/passwd` and `/etc/shadow` Files

### `/etc/passwd` — User Account Information

Every user has a line here. It's readable by everyone.

```bash
cat /etc/passwd | grep alice
```

**Output:**
```
alice:x:1001:1001:Alice Smith,,,:/home/alice:/bin/bash
```

Breaking it down:

```
alice : x  : 1001 : 1001 : Alice Smith,,, : /home/alice : /bin/bash
  │    │     │      │       │                │              │
  │    │     │      │       │                │              └─ Login shell
  │    │     │      │       │                └─ Home directory
  │    │     │      │       └─ User info (full name)
  │    │     │      └─ Primary group ID (GID)
  │    │     └─ User ID (UID)
  │    └─ Password placeholder (x = stored in /etc/shadow)
  └─ Username
```

### `/etc/shadow` — Encrypted Passwords

Passwords are stored here in encrypted form. Only root can read it.

```bash
sudo cat /etc/shadow | grep alice
```

**Output:**
```
alice:$6$rounds=5000$salt$hashedpassword...:19123:0:99999:7:::
```

### `/etc/group` — Group Information

```bash
cat /etc/group | grep developers
```

**Output:**
```
developers:x:1002:alice,bob,charlie
```

```
developers : x    : 1002 : alice,bob,charlie
    │        │      │       │
    │        │      │       └─ Group members
    │        │      └─ Group ID (GID)
    │        └─ Password placeholder
    └─ Group name
```

---

## 12. Special Permissions — setuid, setgid, sticky bit

Beyond the standard `rwx`, Linux has three special permission bits.

### setuid (SUID) — Run as the File Owner

When set on an executable, it runs with the **owner's permissions** instead of the caller's.

```bash
chmod u+s /path/to/program      # Set SUID
chmod 4755 /path/to/program     # Set SUID with numeric mode
```

**Example:** The `passwd` command has SUID set — it lets regular users modify `/etc/shadow` (owned by root) to change their own password.

```bash
ls -la /usr/bin/passwd
# -rwsr-xr-x 1 root root ... /usr/bin/passwd
#     │
#     └─ 's' here means SUID is set
```

### setgid (SGID) — Run as the File's Group

On files: runs with group's permissions.
On directories: new files inside inherit the directory's group.

```bash
chmod g+s /shared/project/      # Set SGID on directory
chmod 2755 /shared/project/     # Numeric mode
```

### Sticky Bit — Protect Files in Shared Directories

Only the **file owner** can delete their own files (even if others have write permission on the directory).

```bash
chmod +t /shared/folder/        # Set sticky bit
chmod 1777 /tmp                 # Typical /tmp permissions
```

```bash
ls -la /tmp
# drwxrwxrwt ... /tmp
#          │
#          └─ 't' means sticky bit is set
```

### Viewing Special Permissions

```bash
ls -la /usr/bin/passwd          # SUID: 's' in owner execute position
ls -la /shared/folder           # SGID: 's' in group execute position
ls -la /tmp                     # Sticky: 't' in others execute position
```

---

## 13. umask — Default Permission Mask

`umask` sets the **default permissions** for newly created files and directories.

```bash
umask                   # Show current umask
```

**Output:**
```
0022
```

### How umask Works

Files start with a maximum of **666** (no execute by default).
Directories start with a maximum of **777**.
The umask value is **subtracted**:

```
Files:       666 - 022 = 644  → rw-r--r--
Directories: 777 - 022 = 755  → rwxr-xr-x
```

### Common umask Values

| umask | New File | New Directory | Use Case |
|-------|----------|---------------|----------|
| `022` | `644` | `755` | Default — others can read |
| `027` | `640` | `750` | Group can read, others nothing |
| `077` | `600` | `700` | Private — only owner |
| `002` | `664` | `775` | Group collaboration |

### Set umask Temporarily

```bash
umask 027               # Change for current session
touch newfile.txt
ls -la newfile.txt
# -rw-r----- (640) — group can read, others nothing
```

### Set umask Permanently

Add to `~/.bashrc`:

```bash
echo "umask 027" >> ~/.bashrc
source ~/.bashrc
```

---

## ✅ Daily Tasks Checklist

- [ ] Run `whoami` and `id` to understand your user identity
- [ ] Run `ls -la` in your home directory and read the permission strings
- [ ] Create a file and check its default permissions with `ls -la`
- [ ] Use `chmod 600` to make a file private
- [ ] Use `chmod +x` to make a script executable
- [ ] Use `chmod 755` on a directory
- [ ] Check who owns a file with `ls -la`
- [ ] Use `sudo` to run a command as root
- [ ] Create a new user with `sudo adduser testuser`
- [ ] Set a password for the new user
- [ ] Create a new group with `sudo groupadd testgroup`
- [ ] Add the new user to the group with `sudo usermod -aG testgroup testuser`
- [ ] Verify with `groups testuser`
- [ ] Delete the test user with `sudo deluser testuser`
- [ ] Check your umask value with `umask`

---

## 🏋️ Practice Exercises

### Exercise 1 — Read and Understand Permissions

```bash
# Check permissions on important files
ls -la /etc/passwd          # World-readable
ls -la /etc/shadow          # Root-only
ls -la /usr/bin/passwd      # SUID bit
ls -la /tmp                 # Sticky bit
ls -la /home                # Home directories

# Check your own files
ls -la ~/
```

For each file, write down:
- File type (`-`, `d`, `l`)
- Owner permissions
- Group permissions
- Others permissions
- Owner name
- Group name

---

### Exercise 2 — chmod Practice

```bash
# Create test files
mkdir ~/permission-practice
cd ~/permission-practice
touch file1.txt file2.txt file3.sh

# Check default permissions
ls -la

# Make file1.txt private (only you can read/write)
chmod 600 file1.txt
ls -la file1.txt

# Make file2.txt read-only for everyone
chmod 444 file2.txt
ls -la file2.txt

# Try to write to the read-only file (should fail)
echo "test" > file2.txt

# Make file3.sh executable
chmod 755 file3.sh
ls -la file3.sh

# Try running it (empty script, will do nothing)
./file3.sh

# Reset to default
chmod 644 file1.txt
chmod 644 file2.txt
```

---

### Exercise 3 — Create and Manage Users and Groups

```bash
# Create a new group called 'project'
sudo groupadd project

# Create two new users
sudo adduser dev1
sudo adduser dev2

# Add both users to the project group
sudo usermod -aG project dev1
sudo usermod -aG project dev2

# Verify their group membership
groups dev1
groups dev2

# Create a shared project directory
sudo mkdir /opt/project-shared
sudo chown root:project /opt/project-shared
sudo chmod 2775 /opt/project-shared   # SGID so new files inherit group

# Check it
ls -la /opt/
```

---

### Exercise 4 — sudo and Root Exploration

```bash
# Check what sudo commands you are allowed to run
sudo -l

# Run a command that requires root
sudo cat /etc/shadow

# Check system-wide user list
sudo cat /etc/passwd | cut -d: -f1 | sort

# Check all groups
cat /etc/group | cut -d: -f1 | sort

# See currently logged in users
who
w
```

---

### Exercise 5 — Build a Permission Scenario

```bash
# Scenario: You are setting up a project for a team
# Goal: Owner can read/write, team members can read, others can't access

# Create the file
echo "Secret project data" > ~/project_data.txt

# Check current permissions
ls -la ~/project_data.txt

# Set owner read/write, group read, others nothing
chmod 640 ~/project_data.txt

# Verify
ls -la ~/project_data.txt
# -rw-r----- ... project_data.txt

# Change group owner to 'project' (if group exists)
# sudo chgrp project ~/project_data.txt

# Verify final result
ls -la ~/project_data.txt
```

---

## 📝 Summary

Today you learned:

| Command | What It Does |
|---------|-------------|
| `whoami` | Show current username |
| `id` | Show UID, GID, and all groups |
| `who` / `w` | Show logged-in users |
| `ls -la` | View file permissions and ownership |
| `chmod 755 file` | Set permissions using numbers |
| `chmod +x file` | Add execute permission |
| `chmod -R 755 dir/` | Apply permissions recursively |
| `chown user file` | Change file owner |
| `chown user:group file` | Change owner and group |
| `chgrp group file` | Change group only |
| `sudo command` | Run command as root |
| `sudo -i` | Open root shell |
| `su username` | Switch to another user |
| `sudo adduser alice` | Create new user (interactive) |
| `sudo passwd alice` | Set/change user password |
| `sudo usermod -aG group user` | Add user to group |
| `sudo deluser alice` | Delete a user |
| `sudo groupadd devs` | Create a new group |
| `sudo groupdel devs` | Delete a group |
| `groups username` | List user's groups |
| `umask` | Show/set default permissions |

### Permission Quick Reference

```
7 = rwx   (read + write + execute)
6 = rw-   (read + write)
5 = r-x   (read + execute)
4 = r--   (read only)
0 = ---   (no permissions)

755 = Owner: all | Group: read+exec | Others: read+exec  → executables
644 = Owner: rw  | Group: read      | Others: read       → regular files
600 = Owner: rw  | Group: none      | Others: none       → private files
777 = Everyone can do everything                         → avoid this!
```

### The Golden Rule of Linux Permissions

> 🔐 **Give the minimum permissions necessary.** A file should only be readable/writable/executable by those who truly need it. This is called the **Principle of Least Privilege**.

---

## 🔜 What's Next — Day 5

Tomorrow you will learn how to **monitor processes and manage system resources** — see what's running, stop it, and install new software.

Preview of Day 5 commands:

```bash
ps aux        # List all running processes
top / htop    # Real-time process monitor
kill          # Stop a process
df -h         # Disk space usage
free -h       # RAM usage
apt           # Install and manage packages
```

---

> 💬 **Stuck?** Run `man chmod`, `man chown`, or `man sudo` for detailed help.
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**Day 4 Complete! 🎉**

⭐ You now understand Linux permissions and user management — a critical skill for every Linux user!

[← Day 3](./Day3_Text_and_Search.md) | [Back to Learning Plan](./LEARNING_PLAN.md) | [Day 5 →](./Day5_Processes_and_System.md)

</div>
