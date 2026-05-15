# 🐧 Day 2 — Files & Directories

> **Goal:** Create, copy, move, rename, and delete files and folders confidently using the Linux terminal.

![Day](https://img.shields.io/badge/Day-2%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Beginner-green?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 1](#1-quick-recap-of-day-1)
2. [Creating Files — `touch`](#2-creating-files--touch)
3. [Creating Directories — `mkdir`](#3-creating-directories--mkdir)
4. [Viewing File Contents — `cat`, `less`, `more`](#4-viewing-file-contents--cat-less-more)
5. [Writing to Files — `echo` & Redirection](#5-writing-to-files--echo--redirection)
6. [Copying Files & Folders — `cp`](#6-copying-files--folders--cp)
7. [Moving & Renaming — `mv`](#7-moving--renaming--mv)
8. [Deleting Files & Folders — `rm` & `rmdir`](#8-deleting-files--folders--rm--rmdir)
9. [Viewing File Details — `ls` Deep Dive](#9-viewing-file-details--ls-deep-dive)
10. [Understanding File Paths](#10-understanding-file-paths)
11. [Daily Tasks Checklist](#-daily-tasks-checklist)
12. [Practice Exercises](#-practice-exercises)
13. [Summary](#-summary)

---

## 1. Quick Recap of Day 1

Before we start, make sure you remember these from Day 1:

```bash
pwd          # Where am I?
ls           # What's here?
cd folder    # Move into a folder
cd ~         # Go home
whoami       # Who am I?
man command  # Help for any command
```

Today we go deeper — we will **create, read, copy, move, and delete** files and directories.

---

## 2. Creating Files — `touch`

The `touch` command creates a **new empty file**. If the file already exists, it updates its timestamp.

### Syntax

```bash
touch filename
```

### Examples

```bash
touch hello.txt               # Create a single file
touch notes.txt readme.txt    # Create multiple files at once
touch my\ file.txt            # File name with a space (use backslash)
touch "my file.txt"           # Or wrap in quotes
```

### Verify it worked

```bash
ls -la
```

**Output:**
```
-rw-r--r-- 1 user user    0 May 14 10:00 hello.txt
-rw-r--r-- 1 user user    0 May 14 10:00 notes.txt
-rw-r--r-- 1 user user    0 May 14 10:00 readme.txt
```

Notice the file size is **0** — the file is empty.

### Create files with specific extensions

```bash
touch script.sh       # Shell script
touch style.css       # CSS file
touch index.html      # HTML file
touch data.json       # JSON file
touch report.md       # Markdown file
```

> 💡 **Tip:** The extension (`.txt`, `.sh`, `.html`) is just a label in Linux. The OS doesn't enforce it like Windows does.

---

## 3. Creating Directories — `mkdir`

`mkdir` stands for **make directory**. It creates a new folder.

### Syntax

```bash
mkdir directory_name
```

### Examples

```bash
mkdir projects                    # Create a single folder
mkdir music photos videos         # Create multiple folders at once
mkdir "My Documents"              # Folder with a space in the name
```

### Create Nested Directories with `-p`

The `-p` flag creates **parent directories** along the way if they don't exist.

```bash
mkdir -p projects/linux/day2
```

This creates:
```
projects/
└── linux/
    └── day2/
```

Without `-p`, this would fail if `projects/` or `linux/` didn't exist yet.

### Verify

```bash
ls
# Output: projects  music  photos  videos

ls projects/linux/
# Output: day2
```

### Useful `mkdir` options

```bash
mkdir -p a/b/c/d        # Create deeply nested folders
mkdir -v new_folder     # Verbose — print what was created
```

**Output of `-v`:**
```
mkdir: created directory 'new_folder'
```

---

## 4. Viewing File Contents — `cat`, `less`, `more`

Once a file has content, you can read it with several commands.

---

### `cat` — Print File Contents

`cat` (concatenate) prints the **entire file** to the terminal at once.

```bash
cat filename.txt
```

**Example:**
```bash
cat hello.txt
```
**Output:**
```
Hello, this is my first Linux file!
Welcome to Day 2.
```

#### Useful `cat` options

```bash
cat -n file.txt        # Show line numbers
cat -A file.txt        # Show hidden characters (tabs, line endings)
cat file1.txt file2.txt  # Print two files one after another
```

> ⚠️ `cat` is great for small files. For large files, use `less`.

---

### `less` — Scroll Through Large Files

`less` lets you scroll through a file **page by page** — it doesn't load everything at once.

```bash
less filename.txt
```

#### Navigation inside `less`

| Key | Action |
|-----|--------|
| `↓` or `j` | Scroll down one line |
| `↑` or `k` | Scroll up one line |
| `Space` | Scroll down one page |
| `b` | Scroll up one page |
| `G` | Jump to end of file |
| `g` | Jump to beginning |
| `/word` | Search for "word" |
| `n` | Next search result |
| `q` | Quit |

---

### `more` — Simple Pager (Older)

Similar to `less` but only scrolls **forward**.

```bash
more filename.txt
```

> 💡 **Prefer `less` over `more`** — it's more powerful. (The saying goes: *"less is more"*)

---

### `head` — View the Beginning of a File

```bash
head file.txt           # Show first 10 lines (default)
head -n 5 file.txt      # Show first 5 lines
head -n 20 file.txt     # Show first 20 lines
```

---

### `tail` — View the End of a File

```bash
tail file.txt           # Show last 10 lines (default)
tail -n 5 file.txt      # Show last 5 lines
tail -f logfile.txt     # Follow file in real time (great for logs!)
```

> 💡 `tail -f` is extremely useful for monitoring **log files** as they update live.

---

## 5. Writing to Files — `echo` & Redirection

You already know `echo` prints text. Now let's use it to **write into files**.

### Output Redirection `>` — Overwrite

The `>` operator sends output **into a file**, replacing any existing content.

```bash
echo "Hello, Linux!" > hello.txt
```

Check the result:
```bash
cat hello.txt
# Output: Hello, Linux!
```

Write again — it **overwrites**:
```bash
echo "This replaces everything" > hello.txt
cat hello.txt
# Output: This replaces everything
```

### Append Redirection `>>` — Add Without Overwriting

The `>>` operator **adds to the end** of a file without deleting existing content.

```bash
echo "Line 1" > notes.txt
echo "Line 2" >> notes.txt
echo "Line 3" >> notes.txt
cat notes.txt
```

**Output:**
```
Line 1
Line 2
Line 3
```

### Write Multiple Lines with `cat` and Heredoc

```bash
cat > story.txt << EOF
Once upon a time,
there was a penguin named Tux.
He loved the terminal.
EOF
```

```bash
cat story.txt
```

**Output:**
```
Once upon a time,
there was a penguin named Tux.
He loved the terminal.
```

---

## 6. Copying Files & Folders — `cp`

`cp` copies a file or directory from one location to another.

### Syntax

```bash
cp source destination
```

### Copy a File

```bash
cp hello.txt hello_backup.txt          # Copy in the same folder
cp hello.txt Documents/hello.txt       # Copy into another folder
cp hello.txt Documents/                # Copy into a folder (keep name)
```

### Copy and Rename at the Same Time

```bash
cp hello.txt Documents/greetings.txt   # Copy + rename
```

### Copy Multiple Files into a Directory

```bash
cp file1.txt file2.txt file3.txt Documents/
```

### Copy a Directory with `-r` (Recursive)

To copy a folder and everything inside it, use `-r`:

```bash
cp -r projects/ projects_backup/
```

> ⚠️ Without `-r`, copying a directory will fail with an error.

### Useful `cp` options

```bash
cp -r folder/ backup/      # Copy directory recursively
cp -v file.txt dest/       # Verbose — show what's being copied
cp -i file.txt dest/       # Interactive — ask before overwriting
cp -u file.txt dest/       # Update — only copy if source is newer
```

**Output of `-v`:**
```
'file.txt' -> 'dest/file.txt'
```

---

## 7. Moving & Renaming — `mv`

`mv` does two things: **move** a file to a new location, or **rename** it. It's the same command for both.

### Syntax

```bash
mv source destination
```

### Rename a File

```bash
mv old_name.txt new_name.txt
```

### Move a File to a Different Directory

```bash
mv hello.txt Documents/            # Move to Documents folder
mv hello.txt Documents/hi.txt      # Move AND rename at the same time
```

### Move Multiple Files

```bash
mv file1.txt file2.txt file3.txt Documents/
```

### Move a Directory

```bash
mv old_folder/ new_folder/         # Rename a directory
mv projects/ Documents/            # Move the whole projects folder
```

### Useful `mv` options

```bash
mv -v file.txt dest/      # Verbose — show what's happening
mv -i file.txt dest/      # Interactive — ask before overwriting
mv -n file.txt dest/      # No overwrite — skip if file exists
```

### The Difference Between `cp` and `mv`

| Command | Effect on Original | Creates Copy |
|---------|-------------------|--------------|
| `cp` | Original stays | ✅ Yes |
| `mv` | Original is removed | ❌ No |

---

## 8. Deleting Files & Folders — `rm` & `rmdir`

> ⚠️ **IMPORTANT:** Linux has **no Recycle Bin** in the terminal. Deleted files are gone permanently. Always double-check before running `rm`.

---

### `rm` — Remove Files

```bash
rm file.txt                   # Delete a single file
rm file1.txt file2.txt        # Delete multiple files
```

### `rm` with options

```bash
rm -v file.txt         # Verbose — confirm what was deleted
rm -i file.txt         # Interactive — ask before deleting
```

**Output of `-i`:**
```
rm: remove regular file 'file.txt'? y
```

### Delete a Directory and Its Contents — `rm -r`

```bash
rm -r folder_name/        # Delete folder and everything inside
```

### Force Delete — `rm -f`

Skips confirmation prompts. Use carefully.

```bash
rm -f file.txt             # Force delete without asking
rm -rf folder_name/        # Force delete folder recursively
```

> 🔴 **`rm -rf` is one of the most dangerous commands in Linux.**
> Never run `rm -rf /` — it deletes the entire system.
> Always double-check the path before running it.

### `rmdir` — Remove Empty Directories Only

```bash
rmdir empty_folder/       # Only works if the folder is empty
```

If the folder has files inside:
```
rmdir: failed to remove 'folder/': Directory not empty
```

Use `rm -r` instead for non-empty folders.

---

## 9. Viewing File Details — `ls` Deep Dive

You used `ls` on Day 1. Today let's fully understand what it shows.

```bash
ls -la
```

**Output:**
```
total 48
drwxr-xr-x 5 user user 4096 May 14 10:00 .
drwxr-xr-x 3 root root 4096 May 13 09:00 ..
-rw-r--r-- 1 user user   28 May 14 10:01 hello.txt
-rw-r--r-- 1 user user   42 May 14 10:05 notes.txt
drwxr-xr-x 2 user user 4096 May 14 10:02 projects
```

### Breaking Down Each Column

```
-rw-r--r--  1  user  user  28  May 14 10:01  hello.txt
│           │  │     │     │   │              │
│           │  │     │     │   │              └─ File name
│           │  │     │     │   └─ Last modified date
│           │  │     │     └─ File size (bytes)
│           │  │     └─ Group owner
│           │  └─ Owner (user)
│           └─ Number of hard links
└─ File type + Permissions
```

### File Type Character

| Character | Meaning |
|-----------|---------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `b` | Block device |
| `c` | Character device |

### Permissions Block

```
rw-r--r--
│││││││││
│││││││└└─ Others: read=yes, write=no, execute=no
││││└└└─── Group: read=yes, write=no, execute=no
│└└└─────── Owner: read=yes, write=yes, execute=no
```

We will go deep into permissions on **Day 4**.

---

## 10. Understanding File Paths

A **path** tells Linux where a file or directory lives.

### Absolute Path

Starts from root `/`. Works from anywhere.

```bash
cd /home/yourname/Documents
cat /home/yourname/notes.txt
ls /etc/
```

### Relative Path

Starts from your **current location**.

```bash
# If you are in /home/yourname:
cd Documents              # Same as cd /home/yourname/Documents
cat notes.txt             # Same as cat /home/yourname/notes.txt
```

### Special Path Symbols

| Symbol | Meaning |
|--------|---------|
| `/` | Root of the file system |
| `~` | Your home directory (`/home/yourname`) |
| `.` | Current directory |
| `..` | Parent directory (one level up) |
| `-` | Previous directory |

```bash
cd .          # Stay in current directory (does nothing)
ls .          # List current directory (same as ls)
ls ..         # List parent directory
cd ../..      # Go up two levels
cp file.txt . # Copy file into current directory
```

---

## ✅ Daily Tasks Checklist

- [ ] Create a folder called `linux-day2` using `mkdir`
- [ ] Navigate into it with `cd linux-day2`
- [ ] Create 3 text files using `touch`
- [ ] Write text into a file using `echo "text" > file.txt`
- [ ] Append a line using `echo "more text" >> file.txt`
- [ ] View the file with `cat`
- [ ] View it with `less` (press `q` to exit)
- [ ] Create a nested folder: `mkdir -p week1/day2/notes`
- [ ] Copy a file using `cp`
- [ ] Move/rename a file using `mv`
- [ ] Delete a file using `rm`
- [ ] Delete an empty folder using `rmdir`
- [ ] Run `ls -la` and read all the columns

---

## 🏋️ Practice Exercises

### Exercise 1 — Build a Project Structure

```bash
# Create this folder structure from your home directory
mkdir -p ~/my-project/src
mkdir -p ~/my-project/docs
mkdir -p ~/my-project/tests

# Create files inside each
touch ~/my-project/src/main.sh
touch ~/my-project/docs/readme.txt
touch ~/my-project/tests/test1.txt

# Verify the structure
ls -R ~/my-project/
```

**Expected output:**
```
/home/yourname/my-project/:
docs  src  tests

/home/yourname/my-project/docs:
readme.txt

/home/yourname/my-project/src:
main.sh

/home/yourname/my-project/tests:
test1.txt
```

---

### Exercise 2 — Write, Read, and Append

```bash
# Write to a file
echo "I am learning Linux" > progress.txt

# Append more lines
echo "Day 1: Introduction done" >> progress.txt
echo "Day 2: Files and Directories" >> progress.txt

# Read the file
cat progress.txt

# Count the lines
wc -l progress.txt

# View with line numbers
cat -n progress.txt
```

---

### Exercise 3 — Copy and Move Practice

```bash
# Create a test file
echo "This is the original" > original.txt

# Copy it
cp original.txt copy.txt

# Verify both exist
ls -l original.txt copy.txt

# Rename the copy
mv copy.txt renamed.txt

# Move renamed.txt into a folder
mkdir archive
mv renamed.txt archive/

# Verify
ls archive/
```

---

### Exercise 4 — Safe Deletion Practice

```bash
# Create some throwaway files
touch junk1.txt junk2.txt junk3.txt
mkdir empty_folder

# Delete one file (interactive — will ask you to confirm)
rm -i junk1.txt

# Delete multiple files
rm junk2.txt junk3.txt

# Delete the empty folder
rmdir empty_folder

# Verify everything is cleaned up
ls
```

---

### Exercise 5 — Explore with `ls`

```bash
# List your home folder in detail
ls -la ~

# List the /etc folder
ls /etc

# List only directories inside /etc
ls -d /etc/*/

# Sort by file size
ls -lS ~/

# Sort by modification time (newest first)
ls -lt ~/
```

---

## 📝 Summary

Today you learned:

| Command | What It Does |
|---------|-------------|
| `touch file.txt` | Create an empty file |
| `mkdir folder` | Create a new directory |
| `mkdir -p a/b/c` | Create nested directories |
| `cat file.txt` | Print file contents |
| `less file.txt` | Scroll through a file |
| `head -n 5 file.txt` | Show first 5 lines |
| `tail -n 5 file.txt` | Show last 5 lines |
| `echo "text" > file` | Write text to file (overwrite) |
| `echo "text" >> file` | Append text to file |
| `cp source dest` | Copy a file |
| `cp -r folder/ dest/` | Copy a folder recursively |
| `mv source dest` | Move or rename a file/folder |
| `rm file.txt` | Delete a file |
| `rm -r folder/` | Delete a folder and its contents |
| `rmdir folder/` | Delete an empty folder |
| `ls -la` | List files with full details |

### Golden Rules for Day 2

> 🟢 Always use `ls` before `rm` to confirm what you're about to delete.
>
> 🟢 Use `cp` before `mv` when you're unsure — keep a backup.
>
> 🔴 Never run `rm -rf` on a path you're not 100% sure about.
>
> 🟢 Use `-i` flag with `rm` and `mv` while you're still learning — it asks before doing.

---

## 🔜 What's Next — Day 3

Tomorrow you will learn how to **search through files and filter output** — one of the most powerful skills in Linux.

Preview of Day 3 commands:

```bash
grep        # Search for text inside files
find        # Locate files anywhere on the system
|           # Pipe: send output of one command into another
>           # Redirect output to a file
wc          # Count words, lines, characters
sort        # Sort output
```

---

> 💬 **Stuck?** Run `man <command>` or `<command> --help` anytime.
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**Day 2 Complete! 🎉**

⭐ You can now create, read, copy, move, and delete files in Linux!

[← Day 1](./Day1_Introduction_and_Setup.md) | [Back to Learning Plan](./LEARNING_PLAN.md) | [Day 3 →](./Day3_Text_and_Search.md)

</div>
