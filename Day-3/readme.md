# 🐧 Day 3 — Text & Search

> **Goal:** Search inside files, find files anywhere on the system, and master the power of pipes and output redirection.

![Day](https://img.shields.io/badge/Day-3%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Beginner--Intermediate-orange?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-1--2%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 2](#1-quick-recap-of-day-2)
2. [grep — Search Inside Files](#2-grep--search-inside-files)
3. [find — Locate Files on Disk](#3-find--locate-files-on-disk)
4. [locate — Fast File Search](#4-locate--fast-file-search)
5. [Pipes — Chaining Commands Together](#5-pipes--chaining-commands-together)
6. [Output Redirection — `>` and `>>`](#6-output-redirection----and-)
7. [Input Redirection — `<`](#7-input-redirection--)
8. [wc — Word & Line Counter](#8-wc--word--line-counter)
9. [sort — Sort Output](#9-sort--sort-output)
10. [uniq — Remove Duplicates](#10-uniq--remove-duplicates)
11. [cut — Extract Columns](#11-cut--extract-columns)
12. [tr — Translate Characters](#12-tr--translate-characters)
13. [Combining It All — Real World Examples](#13-combining-it-all--real-world-examples)
14. [Daily Tasks Checklist](#-daily-tasks-checklist)
15. [Practice Exercises](#-practice-exercises)
16. [Summary](#-summary)

---

## 1. Quick Recap of Day 2

Make sure you remember these from Day 2:

```bash
touch file.txt          # Create a file
mkdir folder            # Create a directory
cat file.txt            # View file contents
echo "text" > file.txt  # Write to a file
echo "text" >> file.txt # Append to a file
cp source dest          # Copy a file
mv source dest          # Move or rename a file
rm file.txt             # Delete a file
```

Today we learn how to **search and filter** — skills you will use every single day as a Linux user.

---

## 2. grep — Search Inside Files

`grep` stands for **Global Regular Expression Print**. It searches for a pattern (text) inside files and prints every line that matches.

### Syntax

```bash
grep "pattern" filename
```

### Basic Example

First, let's create a file to practice with:

```bash
cat > fruits.txt << EOF
apple
banana
grape
pineapple
mango
apricot
blueberry
avocado
EOF
```

Now search for lines containing "apple":

```bash
grep "apple" fruits.txt
```

**Output:**
```
apple
pineapple
apricot
```

All three lines contain the letters "apple" — `grep` matches anywhere in the line.

---

### Common `grep` Options

#### `-i` — Case Insensitive Search

```bash
grep -i "APPLE" fruits.txt
# Matches: apple, pineapple, apricot (ignores uppercase/lowercase)
```

#### `-n` — Show Line Numbers

```bash
grep -n "apple" fruits.txt
```

**Output:**
```
1:apple
4:pineapple
6:apricot
```

#### `-c` — Count Matching Lines

```bash
grep -c "apple" fruits.txt
# Output: 3
```

#### `-v` — Invert Match (Lines that DON'T match)

```bash
grep -v "apple" fruits.txt
```

**Output:**
```
banana
grape
mango
blueberry
avocado
```

#### `-l` — List File Names Only (not the matching lines)

```bash
grep -l "apple" *.txt
# Output: fruits.txt
```

#### `-r` — Recursive Search (search inside all files in a folder)

```bash
grep -r "apple" ./           # Search in current folder and subfolders
grep -r "error" /var/log/    # Search for errors in log files
```

#### `-w` — Whole Word Match

```bash
grep -w "apple" fruits.txt
# Matches: apple  (NOT pineapple or apricot)
```

#### `-A`, `-B`, `-C` — Context Lines

```bash
grep -A 2 "mango" fruits.txt   # Show 2 lines AFTER the match
grep -B 2 "mango" fruits.txt   # Show 2 lines BEFORE the match
grep -C 2 "mango" fruits.txt   # Show 2 lines BEFORE and AFTER
```

#### `--color` — Highlight Matches

```bash
grep --color "apple" fruits.txt
# The word "apple" is highlighted in red
```

---

### Searching in Multiple Files

```bash
grep "apple" fruits.txt veggies.txt     # Search in two files
grep "apple" *.txt                       # Search in all .txt files
grep -r "TODO" ~/projects/               # Find all TODOs in a project
```

---

### Using grep with Regular Expressions

`grep` supports **patterns**, not just plain text:

```bash
grep "^a" fruits.txt        # Lines that START with 'a'
grep "o$" fruits.txt        # Lines that END with 'o'
grep "^.{5}$" fruits.txt    # Lines with exactly 5 characters
grep "a.*e" fruits.txt      # Lines with 'a' followed by 'e'
```

**Output of `grep "^a" fruits.txt`:**
```
apple
apricot
avocado
```

| Pattern | Meaning |
|---------|---------|
| `^` | Start of line |
| `$` | End of line |
| `.` | Any single character |
| `*` | Zero or more of previous |
| `[abc]` | Any of a, b, or c |
| `[^abc]` | Not a, b, or c |

---

## 3. find — Locate Files on Disk

`find` searches for files and directories based on name, type, size, date, and more. It searches the **live file system** in real time.

### Syntax

```bash
find [where to search] [what to look for]
```

---

### Find by Name

```bash
find . -name "fruits.txt"           # Find in current directory
find ~ -name "*.txt"                 # Find all .txt files in home
find / -name "passwd"                # Find everywhere (slow)
find /etc -name "*.conf"             # Find config files in /etc
```

#### Case-insensitive name search

```bash
find . -iname "fruits.txt"          # Matches fruits.txt, Fruits.TXT, etc.
```

---

### Find by Type

```bash
find . -type f          # Find only files
find . -type d          # Find only directories
find . -type l          # Find only symbolic links
```

**Example — Find all directories in home:**

```bash
find ~ -type d
```

---

### Find by Size

```bash
find . -size +1M        # Files larger than 1 MB
find . -size -100k      # Files smaller than 100 KB
find . -size 50k        # Files exactly 50 KB
find /var -size +100M   # Large files consuming disk space
```

| Unit | Meaning |
|------|---------|
| `c` | Bytes |
| `k` | Kilobytes |
| `M` | Megabytes |
| `G` | Gigabytes |

---

### Find by Time

```bash
find . -mtime -1        # Modified in the last 1 day
find . -mtime +7        # Modified more than 7 days ago
find . -mtime 0         # Modified today
find . -newer file.txt  # Modified more recently than file.txt
```

---

### Find by Permissions

```bash
find . -perm 644        # Files with exactly 644 permissions
find . -perm /111       # Files with any execute permission
```

---

### Find and Execute a Command — `-exec`

This is powerful — find files AND do something with them:

```bash
# Find all .txt files and print them with cat
find . -name "*.txt" -exec cat {} \;

# Find all .log files and delete them
find /tmp -name "*.log" -exec rm {} \;

# Find files larger than 100MB and list details
find / -size +100M -exec ls -lh {} \;
```

> 💡 `{}` is a placeholder for the found file. `\;` ends the `-exec` command.

---

### Find Empty Files and Directories

```bash
find . -empty               # Find all empty files and directories
find . -empty -type f       # Find only empty files
find . -empty -type d       # Find only empty directories
```

---

### Combining find Conditions

```bash
# Find .txt files larger than 1KB
find . -name "*.txt" -size +1k

# Find files modified today that are NOT directories
find . -mtime 0 -type f

# Find files owned by a specific user
find /home -user yourname
```

---

## 4. locate — Fast File Search

`locate` finds files using a **pre-built database** — it is much faster than `find` but may not include very recently created files.

```bash
locate fruits.txt           # Find the file fast
locate "*.conf"             # Find all config files
locate -i "readme"          # Case-insensitive search
```

### Update the database

```bash
sudo updatedb               # Refresh the locate database
```

> 💡 Use `find` when you need accuracy and real-time results.
> Use `locate` when you need speed and the file isn't brand new.

---

## 5. Pipes — Chaining Commands Together

A **pipe** (`|`) takes the **output** of one command and sends it as the **input** of another command.

```
command1 | command2 | command3
```

Think of it like an assembly line:

```
[command1] → output → [command2] → output → [command3] → final result
```

---

### Simple Pipe Example

Without pipe:
```bash
ls /etc
# Tons of output scrolling by...
```

With pipe to `less`:
```bash
ls /etc | less
# Now you can scroll through it!
```

---

### Pipe with `grep`

```bash
ls /etc | grep "conf"           # Show only lines containing "conf"
ps aux | grep "bash"            # Find bash processes
cat /etc/passwd | grep "root"   # Find root entry in passwd file
```

---

### Pipe with `wc`

```bash
ls /etc | wc -l                 # Count how many files are in /etc
cat fruits.txt | wc -l          # Count lines in a file
grep "apple" fruits.txt | wc -l # Count how many lines match
```

---

### Chain Multiple Pipes

```bash
cat fruits.txt | grep "a" | sort | head -3
```

This:
1. `cat fruits.txt` — reads the file
2. `grep "a"` — keeps only lines with "a"
3. `sort` — sorts alphabetically
4. `head -3` — shows only the first 3

**Output:**
```
apple
apricot
avocado
```

---

### Pipe with `sort` and `uniq`

```bash
cat fruits.txt | sort | uniq    # Sort and remove duplicates
```

---

## 6. Output Redirection — `>` and `>>`

You already learned `>` and `>>` on Day 2. Let's go deeper.

### `>` — Redirect Output (Overwrite)

```bash
ls > filelist.txt           # Save ls output to a file
echo "Hello" > hello.txt    # Write text to file
```

### `>>` — Redirect Output (Append)

```bash
echo "Line 1" > log.txt
echo "Line 2" >> log.txt
echo "Line 3" >> log.txt
cat log.txt
```

**Output:**
```
Line 1
Line 2
Line 3
```

### Redirect Both stdout and stderr — `2>` and `&>`

Linux has three data streams:

| Stream | Number | Meaning |
|--------|--------|---------|
| stdin | 0 | Input (keyboard) |
| stdout | 1 | Normal output |
| stderr | 2 | Error messages |

```bash
ls /nonexistent 2> errors.txt       # Redirect errors to a file
ls /home > output.txt 2> errors.txt # Redirect output and errors separately
ls /home &> all_output.txt          # Redirect BOTH to same file
ls /home 2>/dev/null                # Discard errors (send to /dev/null)
```

> 💡 `/dev/null` is a special file that discards everything written to it — like a trash bin with no bottom.

---

## 7. Input Redirection — `<`

`<` sends a **file as input** to a command, instead of typing it manually.

```bash
wc -l < fruits.txt          # Count lines, reading from file
sort < fruits.txt            # Sort the file content
grep "apple" < fruits.txt   # Search in file
```

This is equivalent to:
```bash
cat fruits.txt | wc -l
```

---

## 8. wc — Word & Line Counter

`wc` stands for **word count** — it counts lines, words, and characters.

### Syntax

```bash
wc [options] filename
```

### Examples

```bash
wc fruits.txt
```

**Output:**
```
 8  8 58 fruits.txt
 │  │  │
 │  │  └─ Characters (bytes)
 │  └──── Words
 └─────── Lines
```

### Useful Options

```bash
wc -l fruits.txt        # Count lines only
wc -w fruits.txt        # Count words only
wc -c fruits.txt        # Count characters/bytes only
wc -m fruits.txt        # Count characters (multibyte safe)
```

### Count multiple files

```bash
wc -l *.txt             # Line count for all .txt files
```

**Output:**
```
  8 fruits.txt
 12 notes.txt
 20 total
```

---

## 9. sort — Sort Output

`sort` arranges lines of text in a specific order.

### Basic Sort (Alphabetical)

```bash
sort fruits.txt
```

**Output:**
```
apple
apricot
avocado
banana
blueberry
grape
mango
pineapple
```

### Sort Options

```bash
sort -r fruits.txt          # Reverse order (Z to A)
sort -n numbers.txt         # Numeric sort (1, 2, 10 not 1, 10, 2)
sort -u fruits.txt          # Sort and remove duplicates
sort -k2 data.txt           # Sort by 2nd column
sort -t"," -k2 data.csv     # Sort CSV by 2nd column
```

### Example — Sort by Number

Create a file:
```bash
echo -e "10\n2\n30\n5\n1" > numbers.txt
```

Without `-n` (sorts as text):
```bash
sort numbers.txt
# Output: 1, 10, 2, 30, 5  ← WRONG order!
```

With `-n` (sorts as numbers):
```bash
sort -n numbers.txt
# Output: 1, 2, 5, 10, 30  ← Correct!
```

---

## 10. uniq — Remove Duplicates

`uniq` removes or reports **consecutive duplicate lines**. It works best after `sort`.

```bash
# Create a file with duplicates
echo -e "apple\nbanana\napple\nmango\nbanana\nbanana" > dupes.txt

cat dupes.txt
sort dupes.txt | uniq           # Remove duplicates
sort dupes.txt | uniq -c        # Count occurrences
sort dupes.txt | uniq -d        # Show only duplicates
sort dupes.txt | uniq -u        # Show only unique (no duplicates)
```

**Output of `sort dupes.txt | uniq -c`:**
```
      2 apple
      2 banana
      1 mango
```

---

## 11. cut — Extract Columns

`cut` extracts specific **columns or fields** from each line.

### Syntax

```bash
cut -d "delimiter" -f field_number filename
```

### Example with CSV data

```bash
cat > people.csv << EOF
Alice,25,Engineer
Bob,30,Designer
Charlie,28,Developer
EOF
```

```bash
cut -d "," -f 1 people.csv      # Extract first column (names)
cut -d "," -f 2 people.csv      # Extract second column (ages)
cut -d "," -f 1,3 people.csv    # Extract columns 1 and 3
```

**Output of `cut -d "," -f 1 people.csv`:**
```
Alice
Bob
Charlie
```

### Cut by Character Position

```bash
cut -c 1-5 fruits.txt           # Extract first 5 characters of each line
cut -c 3- fruits.txt            # Extract from 3rd character to end
```

---

## 12. tr — Translate Characters

`tr` **replaces or deletes** characters in text. It reads from stdin.

### Syntax

```bash
echo "text" | tr 'old' 'new'
```

### Examples

```bash
echo "hello world" | tr 'a-z' 'A-Z'    # Lowercase to uppercase
echo "HELLO WORLD" | tr 'A-Z' 'a-z'    # Uppercase to lowercase
echo "hello world" | tr ' ' '_'         # Replace spaces with underscores
echo "hello" | tr -d 'l'               # Delete all 'l' characters
cat file.txt | tr -s ' '               # Squeeze multiple spaces into one
```

**Output of `echo "hello world" | tr 'a-z' 'A-Z'`:**
```
HELLO WORLD
```

---

## 13. Combining It All — Real World Examples

This is where Linux becomes truly powerful. Let's see real use cases.

---

### Example 1 — Find the 5 Largest Files in a Directory

```bash
find ~ -type f -size +1k | xargs ls -lh | sort -k5 -rh | head -5
```

Step by step:
1. `find ~ -type f -size +1k` — find files larger than 1KB
2. `xargs ls -lh` — run `ls -lh` on each file found
3. `sort -k5 -rh` — sort by size column, largest first
4. `head -5` — show top 5

---

### Example 2 — Count How Many .txt Files You Have

```bash
find ~ -name "*.txt" | wc -l
```

---

### Example 3 — Find and Display Unique Words in a File

```bash
cat fruits.txt | tr ' ' '\n' | sort | uniq
```

---

### Example 4 — Search Logs for Errors

```bash
grep -i "error" /var/log/syslog | tail -20
```

Shows the last 20 error lines in the system log.

---

### Example 5 — Get Your IP Address Cleanly

```bash
ip a | grep "inet " | grep -v "127.0.0.1"
```

---

### Example 6 — List All Running Processes and Search for One

```bash
ps aux | grep "firefox"
```

---

### Example 7 — Count Words in All Text Files

```bash
cat *.txt | wc -w
```

---

### Example 8 — Find Files Modified Today and Save the List

```bash
find ~ -mtime 0 -type f > todays_files.txt
cat todays_files.txt
```

---

## ✅ Daily Tasks Checklist

- [ ] Create a practice file with at least 10 lines of text
- [ ] Use `grep` to search for a word in that file
- [ ] Use `grep -n` to show line numbers with matches
- [ ] Use `grep -v` to show lines that DON'T match
- [ ] Use `grep -r` to search inside a folder recursively
- [ ] Use `find . -name "*.txt"` to find all text files
- [ ] Use `find . -type d` to list all directories
- [ ] Use `find . -size +1k` to find files by size
- [ ] Pipe `ls` output into `grep` to filter results
- [ ] Pipe `cat file.txt` into `wc -l` to count lines
- [ ] Use `sort` to sort a file alphabetically
- [ ] Use `sort | uniq` to remove duplicate lines
- [ ] Redirect command output to a file using `>`
- [ ] Append output to a file using `>>`
- [ ] Discard error output using `2>/dev/null`

---

## 🏋️ Practice Exercises

### Exercise 1 — Build a Search Playground

```bash
# Create a sample log file
cat > server.log << EOF
2026-05-01 INFO  Server started
2026-05-01 INFO  User alice logged in
2026-05-02 ERROR Database connection failed
2026-05-02 INFO  User bob logged in
2026-05-03 WARNING Disk space low
2026-05-03 ERROR Authentication failed for user eve
2026-05-04 INFO  Backup completed
2026-05-04 ERROR Timeout connecting to API
EOF

# Find all ERROR lines
grep "ERROR" server.log

# Count how many errors
grep -c "ERROR" server.log

# Find all lines that are NOT INFO
grep -v "INFO" server.log

# Show line numbers for WARNINGs
grep -n "WARNING" server.log

# Save all errors to a separate file
grep "ERROR" server.log > errors.log
cat errors.log
```

---

### Exercise 2 — find Mastery

```bash
# Create a folder structure
mkdir -p ~/search-practice/docs
mkdir -p ~/search-practice/scripts
mkdir -p ~/search-practice/data

touch ~/search-practice/docs/notes.txt
touch ~/search-practice/docs/report.md
touch ~/search-practice/scripts/backup.sh
touch ~/search-practice/scripts/deploy.sh
touch ~/search-practice/data/users.csv

# Find all .sh files
find ~/search-practice -name "*.sh"

# Find all files (not directories)
find ~/search-practice -type f

# Find all directories
find ~/search-practice -type d

# Find files modified in the last day
find ~/search-practice -mtime -1
```

---

### Exercise 3 — Pipe Power

```bash
# Create a numbers file
echo -e "42\n7\n100\n3\n55\n7\n42\n18" > numbers.txt

# Sort numerically
cat numbers.txt | sort -n

# Sort and remove duplicates
cat numbers.txt | sort -n | uniq

# Count unique numbers
cat numbers.txt | sort | uniq | wc -l

# Find the largest number
cat numbers.txt | sort -n | tail -1

# Find the smallest number
cat numbers.txt | sort -n | head -1
```

---

### Exercise 4 — Text Processing Pipeline

```bash
# Create a CSV file
cat > employees.csv << EOF
Alice,Engineering,95000
Bob,Marketing,72000
Charlie,Engineering,88000
Diana,HR,65000
Eve,Marketing,79000
Frank,Engineering,91000
EOF

# Extract just the names
cut -d "," -f 1 employees.csv

# Extract just the departments
cut -d "," -f 2 employees.csv

# Find all Engineering employees
grep "Engineering" employees.csv

# Count Engineering employees
grep -c "Engineering" employees.csv

# Sort by name
sort employees.csv

# Get unique departments
cut -d "," -f 2 employees.csv | sort | uniq
```

---

### Exercise 5 — Redirect and Save

```bash
# Save system info to a file
echo "=== System Report ===" > system_report.txt
echo "Date: $(date)" >> system_report.txt
echo "User: $(whoami)" >> system_report.txt
echo "Directory: $(pwd)" >> system_report.txt
echo "Disk Usage:" >> system_report.txt
df -h >> system_report.txt

# View the report
cat system_report.txt
```

---

## 📝 Summary

Today you learned:

| Command | What It Does |
|---------|-------------|
| `grep "word" file` | Search for text inside a file |
| `grep -i` | Case-insensitive search |
| `grep -n` | Show line numbers |
| `grep -v` | Invert — show non-matching lines |
| `grep -r` | Recursive search in folders |
| `grep -c` | Count matching lines |
| `find . -name "*.txt"` | Find files by name |
| `find . -type f` | Find only files |
| `find . -type d` | Find only directories |
| `find . -size +1M` | Find files by size |
| `find . -mtime -1` | Find recently modified files |
| `locate filename` | Fast file search (uses database) |
| `cmd1 \| cmd2` | Pipe output of cmd1 into cmd2 |
| `cmd > file` | Redirect output to file (overwrite) |
| `cmd >> file` | Redirect output to file (append) |
| `2>/dev/null` | Discard error messages |
| `wc -l` | Count lines |
| `wc -w` | Count words |
| `sort` | Sort lines alphabetically |
| `sort -n` | Sort numerically |
| `uniq` | Remove consecutive duplicates |
| `cut -d"," -f1` | Extract a column from CSV |
| `tr 'a-z' 'A-Z'` | Transform characters |

### The Power Formula

```
find files → read them → filter with grep → sort/count → save results
     find  →    cat    →      grep        →  sort/wc   →     >
```

---

## 🔜 What's Next — Day 4

Tomorrow you will learn **Users, Groups, and Permissions** — understanding who owns what and controlling access to files.

Preview of Day 4 commands:

```bash
whoami        # Who are you
id            # User and group IDs
chmod         # Change file permissions
chown         # Change file ownership
sudo          # Run commands as root
adduser       # Create new users
```

---

> 💬 **Stuck?** Run `man grep`, `man find`, or `man sort` for detailed help.
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**Day 3 Complete! 🎉**

⭐ You can now search, filter, and process text like a pro!

[← Day 2](./Day2_Files_and_Directories.md) | [Back to Learning Plan](./LEARNING_PLAN.md) | [Day 4 →](./Day4_Users_and_Permissions.md)

</div>
