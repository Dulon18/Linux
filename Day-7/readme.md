# 🐧 Day 7 — Shell Scripting

> **Goal:** Write bash scripts to automate tasks, combine everything you've learned, and finish your 7-day Linux journey strong.

![Day](https://img.shields.io/badge/Day-7%20of%207-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Level](https://img.shields.io/badge/Level-Intermediate-orange?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-2--3%20Hours-blue?style=for-the-badge)

---

## 📋 Table of Contents

1. [Quick Recap of Day 6](#1-quick-recap-of-day-6)
2. [What is Shell Scripting?](#2-what-is-shell-scripting)
3. [Your First Bash Script](#3-your-first-bash-script)
4. [Variables](#4-variables)
5. [User Input — read](#5-user-input--read)
6. [Conditionals — if / elif / else](#6-conditionals--if--elif--else)
7. [Comparison Operators](#7-comparison-operators)
8. [Loops — for](#8-loops--for)
9. [Loops — while](#9-loops--while)
10. [Loops — until](#10-loops--until)
11. [Functions](#11-functions)
12. [Arrays](#12-arrays)
13. [String Operations](#13-string-operations)
14. [Exit Codes & Error Handling](#14-exit-codes--error-handling)
15. [Working with Files in Scripts](#15-working-with-files-in-scripts)
16. [Command Substitution](#16-command-substitution)
17. [Arithmetic in Bash](#17-arithmetic-in-bash)
18. [Cron Jobs — Schedule Scripts](#18-cron-jobs--schedule-scripts)
19. [Real-World Script Examples](#19-real-world-script-examples)
20. [Daily Tasks Checklist](#-daily-tasks-checklist)
21. [Practice Exercises](#-practice-exercises)
22. [Summary](#-summary)
23. [You Did It — What's Next?](#-you-did-it--whats-next)

---

## 1. Quick Recap of Day 6

Make sure you remember these from Day 6:

```bash
ip a                        # View IP addresses
ping -c 4 google.com        # Test connectivity
curl https://ipinfo.io/ip   # Get public IP
wget URL                    # Download a file
ss -tulpn                   # View open ports
ssh user@host               # Connect to remote server
ssh-keygen -t ed25519       # Generate SSH key pair
scp file user@host:~/       # Copy file to remote server
rsync -avzP src/ dest/      # Sync files efficiently
```

Today is the **final day** — we put everything together with the superpower of automation: **Shell Scripting**.

---

## 2. What is Shell Scripting?

A **shell script** is a plain text file containing a series of commands that the shell (Bash) executes one by one — just as if you typed them yourself.

### Why Write Scripts?

```
Without scripting:                With scripting:
─────────────────                 ───────────────
Type command 1                    Run one script
Type command 2                    ↓
Type command 3          →         All 20 commands execute
...                               automatically, perfectly,
Type command 20                   every time
```

### Real Use Cases

- **Automated backups** — back up files every night
- **System health checks** — check disk/memory and alert
- **Deployment scripts** — set up a server in one command
- **Log cleanup** — delete old log files automatically
- **Batch file processing** — rename 1000 files at once
- **Monitoring** — check if a website is down and send alerts

---

## 3. Your First Bash Script

### Step 1 — Create the Script File

```bash
nano hello.sh
```

### Step 2 — Write the Script

```bash
#!/bin/bash
# My first bash script
# The line above is called a "shebang" — it tells Linux to use bash

echo "Hello, Linux World!"
echo "Today is: $(date)"
echo "You are logged in as: $(whoami)"
echo "Your current directory is: $(pwd)"
```

### Step 3 — Save and Exit Nano

Press `Ctrl + O` → `Enter` → `Ctrl + X`

### Step 4 — Make it Executable

```bash
chmod +x hello.sh
```

### Step 5 — Run it

```bash
./hello.sh
```

**Output:**
```
Hello, Linux World!
Today is: Thu May 14 10:00:00 UTC 2026
You are logged in as: yourname
Your current directory is: /home/yourname
```

### The Shebang Line

```bash
#!/bin/bash
```

| Part | Meaning |
|------|---------|
| `#!` | Shebang — tells the OS this is a script |
| `/bin/bash` | Path to the interpreter (bash) |

Always put this as the **very first line** of every script.

### Comments

```bash
# This is a single-line comment — ignored by bash
echo "This runs"   # Inline comment — everything after # is ignored
```

---

## 4. Variables

Variables store data you want to reuse in your script.

### Defining Variables

```bash
#!/bin/bash

NAME="Alice"
AGE=25
CITY="Dhaka"
PI=3.14159
```

> ⚠️ **No spaces around `=`**
> `NAME="Alice"` ✅
> `NAME = "Alice"` ❌ — this causes an error

### Using Variables

Add `$` before the variable name to use its value:

```bash
echo "Name: $NAME"
echo "Age: $AGE"
echo "City: $CITY"

# Or use curly braces (safer, especially next to other text)
echo "My name is ${NAME}!"
echo "I live in ${CITY}, Bangladesh."
```

**Output:**
```
Name: Alice
Age: 25
City: Dhaka
My name is Alice!
I live in Dhaka, Bangladesh.
```

### Variable Naming Rules

```bash
my_name="Alice"      # ✅ lowercase with underscore (recommended)
MyName="Alice"       # ✅ CamelCase
MYNAME="Alice"       # ✅ UPPERCASE (used for constants/environment)
my-name="Alice"      # ❌ hyphens not allowed
2name="Alice"        # ❌ cannot start with a number
```

### Special Built-in Variables

```bash
$0          # Name of the script itself
$1, $2, $3  # Command-line arguments (1st, 2nd, 3rd)
$#          # Number of arguments passed
$@          # All arguments as a list
$?          # Exit code of the last command
$$          # PID (Process ID) of current script
$USER       # Current username (environment variable)
$HOME       # Home directory path
$PATH       # List of directories to search for commands
$HOSTNAME   # Machine's hostname
```

**Example using arguments:**

```bash
#!/bin/bash
echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Total arguments: $#"
echo "All arguments: $@"
```

Run it:

```bash
./myscript.sh hello world
```

**Output:**
```
Script name: ./myscript.sh
First argument: hello
Second argument: world
Total arguments: 2
All arguments: hello world
```

### Environment Variables

```bash
echo $HOME          # /home/yourname
echo $USER          # yourname
echo $PATH          # /usr/local/bin:/usr/bin:/bin:...
echo $SHELL         # /bin/bash
printenv            # Print ALL environment variables
```

### Readonly Variables (Constants)

```bash
readonly MAX_RETRIES=3
MAX_RETRIES=5   # Error: MAX_RETRIES is readonly
```

---

## 5. User Input — read

The `read` command pauses and waits for the user to type something.

### Basic Input

```bash
#!/bin/bash

echo "What is your name?"
read NAME
echo "Hello, $NAME! Welcome to Linux scripting."
```

### Inline Prompt with `-p`

```bash
read -p "Enter your name: " NAME
echo "Hello, $NAME!"
```

### Silent Input (for passwords) with `-s`

```bash
read -s -p "Enter your password: " PASSWORD
echo ""   # print a newline since -s hides input
echo "Password saved (${#PASSWORD} characters)"
```

### Read with a Timeout

```bash
read -t 10 -p "Enter something (10 sec timeout): " INPUT
if [ -z "$INPUT" ]; then
    echo "No input received — using default"
    INPUT="default"
fi
echo "Value: $INPUT"
```

### Read Multiple Values

```bash
read -p "Enter first and last name: " FIRST LAST
echo "First: $FIRST"
echo "Last: $LAST"
```

---

## 6. Conditionals — if / elif / else

Make decisions in your scripts based on conditions.

### Basic if Statement

```bash
#!/bin/bash

AGE=20

if [ $AGE -ge 18 ]; then
    echo "You are an adult."
fi
```

### if / else

```bash
AGE=15

if [ $AGE -ge 18 ]; then
    echo "You are an adult."
else
    echo "You are a minor."
fi
```

### if / elif / else

```bash
SCORE=75

if [ $SCORE -ge 90 ]; then
    echo "Grade: A"
elif [ $SCORE -ge 80 ]; then
    echo "Grade: B"
elif [ $SCORE -ge 70 ]; then
    echo "Grade: C"
elif [ $SCORE -ge 60 ]; then
    echo "Grade: D"
else
    echo "Grade: F"
fi
```

### Important Syntax Rules

```bash
# CORRECT — spaces inside brackets
if [ $AGE -ge 18 ]; then

# WRONG — no spaces
if [$AGE -ge 18]; then      # ❌ Error!
if [ $AGE -ge 18 ];then     # ❌ Error!
```

### One-liner if

```bash
[ $AGE -ge 18 ] && echo "Adult" || echo "Minor"
```

---

## 7. Comparison Operators

### Numeric Comparisons

| Operator | Meaning | Example |
|----------|---------|---------|
| `-eq` | Equal to | `[ $a -eq $b ]` |
| `-ne` | Not equal to | `[ $a -ne $b ]` |
| `-lt` | Less than | `[ $a -lt $b ]` |
| `-le` | Less than or equal | `[ $a -le $b ]` |
| `-gt` | Greater than | `[ $a -gt $b ]` |
| `-ge` | Greater than or equal | `[ $a -ge $b ]` |

### String Comparisons

| Operator | Meaning | Example |
|----------|---------|---------|
| `=` or `==` | Equal | `[ "$a" = "$b" ]` |
| `!=` | Not equal | `[ "$a" != "$b" ]` |
| `-z` | String is empty | `[ -z "$a" ]` |
| `-n` | String is not empty | `[ -n "$a" ]` |

```bash
NAME="Alice"

if [ "$NAME" = "Alice" ]; then
    echo "Hello, Alice!"
fi

if [ -z "$NAME" ]; then
    echo "Name is empty"
fi

if [ -n "$NAME" ]; then
    echo "Name is not empty: $NAME"
fi
```

### File / Directory Tests

| Operator | Meaning |
|----------|---------|
| `-f file` | File exists and is a regular file |
| `-d dir` | Directory exists |
| `-e path` | Path exists (file or directory) |
| `-r file` | File is readable |
| `-w file` | File is writable |
| `-x file` | File is executable |
| `-s file` | File exists and is not empty |

```bash
FILE="data.txt"

if [ -f "$FILE" ]; then
    echo "$FILE exists"
else
    echo "$FILE does not exist"
fi

if [ -r "$FILE" ]; then
    echo "$FILE is readable"
fi
```

### Logical Operators

```bash
# AND — both conditions must be true
if [ $AGE -ge 18 ] && [ $AGE -le 65 ]; then
    echo "Working age adult"
fi

# OR — at least one condition must be true
if [ "$NAME" = "admin" ] || [ "$NAME" = "root" ]; then
    echo "Privileged user"
fi

# NOT — reverse the condition
if ! [ -f "config.txt" ]; then
    echo "Config file missing!"
fi
```

---

## 8. Loops — for

`for` loops repeat commands over a list of items.

### Loop Over a List

```bash
#!/bin/bash

for FRUIT in apple banana mango orange grape; do
    echo "Fruit: $FRUIT"
done
```

**Output:**
```
Fruit: apple
Fruit: banana
Fruit: mango
Fruit: orange
Fruit: grape
```

### Loop Over a Number Range

```bash
# Method 1 — brace expansion
for i in {1..5}; do
    echo "Count: $i"
done

# Method 2 — C-style loop
for (( i=1; i<=5; i++ )); do
    echo "Count: $i"
done

# Count down
for i in {10..1}; do
    echo "Countdown: $i"
done

# Step by 2
for i in {0..10..2}; do
    echo "Even: $i"
done
```

### Loop Over Files

```bash
# Process every .txt file in current directory
for FILE in *.txt; do
    echo "Processing: $FILE"
    wc -l "$FILE"
done

# Loop over files found by find
for FILE in $(find ~ -name "*.sh"); do
    echo "Script found: $FILE"
done
```

### Loop Over Command Output

```bash
# Loop through each line of a file
for LINE in $(cat fruits.txt); do
    echo "Item: $LINE"
done

# Loop through directory listing
for DIR in $(ls -d */); do
    echo "Directory: $DIR"
done
```

### Loop Over Arguments

```bash
#!/bin/bash
# Run: ./script.sh file1.txt file2.txt file3.txt

for ARG in "$@"; do
    echo "Processing argument: $ARG"
done
```

---

## 9. Loops — while

`while` loops keep running as long as a condition is **true**.

### Basic while Loop

```bash
#!/bin/bash

COUNT=1

while [ $COUNT -le 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))      # Increment by 1
done
```

**Output:**
```
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

### Read a File Line by Line

```bash
#!/bin/bash

while IFS= read -r LINE; do
    echo "Line: $LINE"
done < fruits.txt
```

> 💡 This is the **correct** way to read files line by line in bash. `IFS=` preserves leading spaces. `-r` prevents backslash escaping.

### Infinite Loop with Break

```bash
#!/bin/bash

while true; do
    read -p "Type 'quit' to exit: " INPUT
    if [ "$INPUT" = "quit" ]; then
        echo "Goodbye!"
        break
    fi
    echo "You typed: $INPUT"
done
```

### while with Continue (Skip an Iteration)

```bash
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        continue    # Skip number 5
    fi
    echo "Number: $i"
done
```

---

## 10. Loops — until

`until` loops run as long as the condition is **false** (opposite of while).

```bash
#!/bin/bash

COUNT=1

until [ $COUNT -gt 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done
```

Same output as the while example — use whichever reads more naturally.

---

## 11. Functions

Functions group commands into a **reusable block** with a name.

### Define and Call a Function

```bash
#!/bin/bash

# Define the function
greet() {
    echo "Hello, World!"
}

# Call the function
greet
greet    # Call it again
```

### Function with Arguments

```bash
greet_user() {
    echo "Hello, $1!"           # $1 = first argument
    echo "You are $2 years old."   # $2 = second argument
}

greet_user "Alice" 25
greet_user "Bob" 30
```

**Output:**
```
Hello, Alice!
You are 25 years old.
Hello, Bob!
You are 30 years old.
```

### Function with Return Value

```bash
add_numbers() {
    local RESULT=$(( $1 + $2 ))
    echo $RESULT            # "return" value via echo
}

SUM=$(add_numbers 10 25)
echo "Sum: $SUM"            # Output: Sum: 35
```

### Local Variables in Functions

```bash
my_function() {
    local LOCAL_VAR="I am local"    # Only exists inside the function
    GLOBAL_VAR="I am global"        # Accessible outside the function
    echo "$LOCAL_VAR"
}

my_function
echo "$GLOBAL_VAR"      # Works
echo "$LOCAL_VAR"       # Empty — variable is gone after function exits
```

> ✅ Always use `local` for variables inside functions to avoid polluting the global scope.

### Function Best Practices

```bash
#!/bin/bash

# ─── Functions ─────────────────────────────────────

check_disk_space() {
    local THRESHOLD=$1
    local USAGE=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

    if [ "$USAGE" -gt "$THRESHOLD" ]; then
        echo "⚠️  WARNING: Disk usage is ${USAGE}% (threshold: ${THRESHOLD}%)"
        return 1
    else
        echo "✅ Disk usage is ${USAGE}% — OK"
        return 0
    fi
}

# ─── Main ──────────────────────────────────────────

check_disk_space 80
```

---

## 12. Arrays

Arrays store multiple values in a single variable.

### Define an Array

```bash
FRUITS=("apple" "banana" "mango" "orange")
NUMBERS=(10 20 30 40 50)
```

### Access Elements

```bash
echo ${FRUITS[0]}       # First element: apple
echo ${FRUITS[1]}       # Second element: banana
echo ${FRUITS[-1]}      # Last element: orange
echo ${FRUITS[@]}       # All elements
echo ${#FRUITS[@]}      # Number of elements: 4
```

### Loop Over an Array

```bash
for FRUIT in "${FRUITS[@]}"; do
    echo "Fruit: $FRUIT"
done
```

### Add and Remove Elements

```bash
FRUITS+=("grape")           # Add to end
unset FRUITS[1]             # Remove element at index 1
echo ${FRUITS[@]}           # apple mango orange grape
```

### Slice an Array

```bash
echo ${FRUITS[@]:1:2}       # Elements from index 1, take 2
```

---

## 13. String Operations

### String Length

```bash
NAME="Alice"
echo ${#NAME}               # Output: 5
```

### Substring Extraction

```bash
TEXT="Hello, Linux!"
echo ${TEXT:7}              # From position 7: Linux!
echo ${TEXT:7:5}            # From 7, take 5: Linux
echo ${TEXT: -6}            # Last 6 characters: Linux!
```

### String Replacement

```bash
TEXT="I love cats and cats"
echo ${TEXT/cats/dogs}      # Replace first: I love dogs and cats
echo ${TEXT//cats/dogs}     # Replace all:   I love dogs and dogs
```

### Convert Case

```bash
NAME="hello world"
echo ${NAME^^}              # Uppercase: HELLO WORLD
echo ${NAME^}               # Capitalize first: Hello world

NAME="HELLO WORLD"
echo ${NAME,,}              # Lowercase: hello world
```

### Check if String Contains a Substring

```bash
SENTENCE="I love Linux scripting"

if [[ "$SENTENCE" == *"Linux"* ]]; then
    echo "Found 'Linux' in the sentence"
fi
```

### String Trimming (Default Values)

```bash
# Use default if variable is empty
NAME=""
echo ${NAME:-"Default Name"}    # Output: Default Name

# Set variable if empty
echo ${NAME:="Alice"}           # Sets NAME to Alice if empty
```

---

## 14. Exit Codes & Error Handling

Every command returns an **exit code** when it finishes:
- `0` = Success
- `1` (or any non-zero) = Failure

### Check Exit Code

```bash
ls /tmp
echo "Exit code: $?"        # 0 = success

ls /nonexistent
echo "Exit code: $?"        # 2 = failure (directory not found)
```

### Exit from a Script

```bash
#!/bin/bash

echo "Starting script..."

if ! command -v git &>/dev/null; then
    echo "Error: git is not installed"
    exit 1              # Exit with failure code
fi

echo "git is available"
exit 0                  # Exit with success code
```

### Stop Script on Any Error — `set -e`

```bash
#!/bin/bash
set -e      # Exit immediately if any command fails

echo "Step 1"
cp nonexistent.txt /tmp/   # This fails → script stops here
echo "Step 2"              # Never reached
```

### Strict Mode — Best Practice

```bash
#!/bin/bash
set -euo pipefail
# -e : exit on error
# -u : treat unset variables as error
# -o pipefail : catch errors in pipes
```

### Trap — Cleanup on Exit

```bash
#!/bin/bash

TEMP_FILE=$(mktemp)

# This runs when script exits (success or failure)
trap "rm -f $TEMP_FILE; echo 'Cleaned up!'" EXIT

echo "Working with temp file: $TEMP_FILE"
echo "Some data" > $TEMP_FILE
cat $TEMP_FILE

# Temp file is automatically deleted when script ends
```

### Error Handling Function

```bash
#!/bin/bash

error_exit() {
    echo "❌ ERROR: $1" >&2    # Print to stderr
    exit 1
}

FILE="important.txt"

if [ ! -f "$FILE" ]; then
    error_exit "File '$FILE' not found!"
fi

echo "Processing $FILE..."
```

---

## 15. Working with Files in Scripts

### Check if File or Directory Exists

```bash
#!/bin/bash

FILE="data.txt"
DIR="backups"

# Check file
if [ -f "$FILE" ]; then
    echo "✅ File exists: $FILE"
else
    echo "❌ File not found: $FILE"
    touch "$FILE"
    echo "Created empty file: $FILE"
fi

# Check directory
if [ ! -d "$DIR" ]; then
    mkdir -p "$DIR"
    echo "Created directory: $DIR"
fi
```

### Read a Config File

```bash
#!/bin/bash
# config.txt contains: KEY=VALUE pairs

while IFS='=' read -r KEY VALUE; do
    # Skip comments and empty lines
    [[ "$KEY" =~ ^#.*$ ]] && continue
    [[ -z "$KEY" ]] && continue

    echo "Setting: $KEY = $VALUE"
    export "$KEY"="$VALUE"
done < config.txt
```

### Write to a Log File

```bash
#!/bin/bash

LOG_FILE="script.log"
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

log() {
    echo "[$TIMESTAMP] $1" | tee -a "$LOG_FILE"
}

log "Script started"
log "Processing files..."
log "Script completed"
```

---

## 16. Command Substitution

Capture the output of a command and use it as a value.

```bash
# Modern syntax (recommended)
TODAY=$(date)
USER_NAME=$(whoami)
FILE_COUNT=$(ls | wc -l)
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}')

echo "Today: $TODAY"
echo "User: $USER_NAME"
echo "Files here: $FILE_COUNT"
echo "Disk used: $DISK_USAGE"
```

### Nest Command Substitutions

```bash
NEWEST_FILE=$(ls -t $(pwd) | head -1)
echo "Newest file: $NEWEST_FILE"
```

### Use in Strings

```bash
echo "You have $(ls | wc -l) files in $(pwd)"
echo "System uptime: $(uptime -p)"
echo "Memory free: $(free -h | awk '/^Mem/{print $4}')"
```

---

## 17. Arithmetic in Bash

### Using `(( ))` — Integer Arithmetic

```bash
A=10
B=3

echo $(( A + B ))       # 13
echo $(( A - B ))       # 7
echo $(( A * B ))       # 30
echo $(( A / B ))       # 3  (integer division)
echo $(( A % B ))       # 1  (remainder/modulus)
echo $(( A ** B ))      # 1000 (exponent)
```

### Increment and Decrement

```bash
COUNT=0

(( COUNT++ ))           # Increment by 1
(( COUNT += 5 ))        # Add 5
(( COUNT *= 2 ))        # Multiply by 2
echo $COUNT             # 12

(( COUNT-- ))           # Decrement by 1
```

### Using `let`

```bash
let RESULT=10+5
echo $RESULT            # 15
```

### Floating Point — Use `bc`

Bash only does integer math natively. Use `bc` for decimals:

```bash
echo "scale=2; 10 / 3" | bc      # Output: 3.33
echo "scale=4; 22 / 7" | bc      # Output: 3.1428 (pi approximation)

# Calculate percentage
USED=75
TOTAL=100
PERCENT=$(echo "scale=1; $USED * 100 / $TOTAL" | bc)
echo "Used: ${PERCENT}%"
```

---

## 18. Cron Jobs — Schedule Scripts

**Cron** is Linux's task scheduler. It runs commands or scripts automatically at specified times.

### Open the Cron Editor

```bash
crontab -e
```

### Cron Syntax

```
* * * * * /path/to/command
│ │ │ │ │
│ │ │ │ └── Day of week (0=Sun, 1=Mon, ..., 6=Sat)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

### Common Cron Examples

```bash
# Run every minute
* * * * * /home/user/script.sh

# Run every day at midnight
0 0 * * * /home/user/backup.sh

# Run every day at 9:30 AM
30 9 * * * /home/user/report.sh

# Run every Monday at 8 AM
0 8 * * 1 /home/user/weekly.sh

# Run on the 1st of every month at noon
0 12 1 * * /home/user/monthly.sh

# Run every 5 minutes
*/5 * * * * /home/user/check.sh

# Run every hour
0 * * * * /home/user/hourly.sh

# Run at 6 AM and 6 PM every day
0 6,18 * * * /home/user/twice_daily.sh
```

### Cron Special Shortcuts

```bash
@reboot     /home/user/startup.sh      # Run at system startup
@hourly     /home/user/script.sh       # Run every hour
@daily      /home/user/script.sh       # Run once a day
@weekly     /home/user/script.sh       # Run once a week
@monthly    /home/user/script.sh       # Run once a month
```

### View and Manage Cron Jobs

```bash
crontab -l          # List your current cron jobs
crontab -e          # Edit your cron jobs
crontab -r          # Remove ALL your cron jobs (be careful!)
```

### Important: Always Use Full Paths in Cron

```bash
# ❌ WRONG — cron doesn't know your PATH
* * * * * backup.sh

# ✅ CORRECT — use full path
* * * * * /home/yourname/scripts/backup.sh

# ✅ Also redirect output to a log
0 2 * * * /home/yourname/scripts/backup.sh >> /home/yourname/backup.log 2>&1
```

---

## 19. Real-World Script Examples

### Script 1 — Automated Backup

```bash
#!/bin/bash
# backup.sh — Back up a folder with a timestamp

set -euo pipefail

# ─── Config ────────────────────────────────────────
SOURCE_DIR="$HOME/Documents"
BACKUP_DIR="$HOME/backups"
TIMESTAMP=$(date "+%Y-%m-%d_%H-%M-%S")
BACKUP_NAME="backup_${TIMESTAMP}.tar.gz"
LOG_FILE="$HOME/backup.log"

# ─── Functions ─────────────────────────────────────
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# ─── Main ──────────────────────────────────────────
log "Starting backup..."

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR"

# Create compressed archive
if tar -czf "${BACKUP_DIR}/${BACKUP_NAME}" "$SOURCE_DIR"; then
    log "✅ Backup created: ${BACKUP_DIR}/${BACKUP_NAME}"
    log "Size: $(du -sh "${BACKUP_DIR}/${BACKUP_NAME}" | cut -f1)"
else
    log "❌ Backup FAILED!"
    exit 1
fi

# Keep only the last 7 backups
BACKUP_COUNT=$(ls "$BACKUP_DIR"/backup_*.tar.gz 2>/dev/null | wc -l)
if [ "$BACKUP_COUNT" -gt 7 ]; then
    ls -t "$BACKUP_DIR"/backup_*.tar.gz | tail -n +8 | xargs rm -f
    log "🗑️  Old backups cleaned up"
fi

log "Backup complete!"
```

---

### Script 2 — System Health Check

```bash
#!/bin/bash
# health_check.sh — Check system health and display a report

echo "╔══════════════════════════════════════╗"
echo "║         SYSTEM HEALTH REPORT         ║"
echo "╚══════════════════════════════════════╝"
echo ""

# ─── Hostname & Uptime ──────────────────
echo "🖥️  Hostname : $(hostname)"
echo "⏱️  Uptime   : $(uptime -p)"
echo "📅 Date     : $(date)"
echo ""

# ─── CPU Usage ──────────────────────────
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d. -f1)
echo "⚡ CPU Usage : ${CPU_USAGE}%"

# ─── Memory Usage ───────────────────────
TOTAL_MEM=$(free -h | awk '/^Mem/{print $2}')
USED_MEM=$(free -h | awk '/^Mem/{print $3}')
FREE_MEM=$(free -h | awk '/^Mem/{print $4}')
echo "🧠 Memory   : Used ${USED_MEM} / Total ${TOTAL_MEM} (Free: ${FREE_MEM})"

# ─── Disk Usage ─────────────────────────
DISK_USAGE=$(df -h / | awk 'NR==2{print $5}' | tr -d '%')
DISK_USED=$(df -h / | awk 'NR==2{print $3}')
DISK_TOTAL=$(df -h / | awk 'NR==2{print $2}')

echo "💾 Disk     : ${DISK_USED} used of ${DISK_TOTAL} (${DISK_USAGE}%)"

if [ "$DISK_USAGE" -gt 90 ]; then
    echo "   ⚠️  WARNING: Disk is almost full!"
elif [ "$DISK_USAGE" -gt 75 ]; then
    echo "   ⚠️  Disk usage is high"
else
    echo "   ✅ Disk usage is healthy"
fi

# ─── Network ────────────────────────────
echo ""
echo "🌐 Network Interfaces:"
ip -4 a | grep inet | awk '{print "   " $2 " on " $NF}'

# ─── Top 5 Processes ────────────────────
echo ""
echo "🔝 Top 5 Processes by CPU:"
ps aux --sort=-%cpu | awk 'NR>1{printf "   %-20s %s%%\n", $11, $3}' | head -5

echo ""
echo "══════════════════════════════════════"
echo "Report generated at: $(date)"
```

---

### Script 3 — User Setup Script

```bash
#!/bin/bash
# setup_user.sh — Create a new user with basic config

if [ "$EUID" -ne 0 ]; then
    echo "❌ Please run this script as root: sudo ./setup_user.sh"
    exit 1
fi

read -p "Enter new username: " NEW_USER
read -p "Enter full name: " FULL_NAME

# Check if user already exists
if id "$NEW_USER" &>/dev/null; then
    echo "❌ User '$NEW_USER' already exists!"
    exit 1
fi

# Create the user
useradd -m -c "$FULL_NAME" -s /bin/bash "$NEW_USER"
echo "✅ User '$NEW_USER' created"

# Set password
echo "Set password for $NEW_USER:"
passwd "$NEW_USER"

# Create standard directories
sudo -u "$NEW_USER" mkdir -p /home/"$NEW_USER"/{Documents,Downloads,scripts}
echo "✅ Home directories created"

echo ""
echo "✅ Setup complete for user: $NEW_USER"
echo "   Full name: $FULL_NAME"
echo "   Home: /home/$NEW_USER"
echo "   Shell: /bin/bash"
```

---

### Script 4 — Batch File Renamer

```bash
#!/bin/bash
# rename_files.sh — Rename all .txt files to .md in current directory

COUNT=0

for FILE in *.txt; do
    # Check if any .txt files exist
    [ -f "$FILE" ] || { echo "No .txt files found."; exit 0; }

    NEW_NAME="${FILE%.txt}.md"       # Remove .txt, add .md
    mv "$FILE" "$NEW_NAME"
    echo "Renamed: $FILE → $NEW_NAME"
    (( COUNT++ ))
done

echo "Done! Renamed $COUNT file(s)."
```

---

### Script 5 — Website Monitor

```bash
#!/bin/bash
# monitor.sh — Check if websites are up

WEBSITES=("https://google.com" "https://github.com" "https://example.com")
LOG_FILE="monitor.log"
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

echo "[$TIMESTAMP] Checking websites..." | tee -a "$LOG_FILE"

for URL in "${WEBSITES[@]}"; do
    HTTP_CODE=$(curl -o /dev/null -s -w "%{http_code}" --max-time 10 "$URL")

    if [ "$HTTP_CODE" -eq 200 ]; then
        STATUS="✅ UP   (HTTP $HTTP_CODE)"
    else
        STATUS="❌ DOWN (HTTP $HTTP_CODE)"
    fi

    echo "  $STATUS — $URL" | tee -a "$LOG_FILE"
done
```

---

## ✅ Daily Tasks Checklist

- [ ] Create and run your first bash script (`hello.sh`)
- [ ] Write a script that accepts command-line arguments (`$1`, `$2`)
- [ ] Use `read` to get input from the user
- [ ] Write an `if/elif/else` statement comparing numbers
- [ ] Write an `if` statement checking if a file exists
- [ ] Write a `for` loop that counts from 1 to 10
- [ ] Write a `while` loop with a counter
- [ ] Read a file line by line using a `while` loop
- [ ] Define a function and call it with arguments
- [ ] Use command substitution `$()` to capture output
- [ ] Perform arithmetic with `$(( ))`
- [ ] Add `set -euo pipefail` to a script
- [ ] Write a script that creates a log file with timestamps
- [ ] Add a cron job using `crontab -e`
- [ ] Write one of the real-world scripts (backup, health check, etc.)

---

## 🏋️ Practice Exercises

### Exercise 1 — Greeting Script

```bash
#!/bin/bash
# greet.sh — Greet the user based on time of day

HOUR=$(date +%H)    # Get current hour (0-23)
NAME=""

read -p "What is your name? " NAME

if [ -z "$NAME" ]; then
    NAME="stranger"
fi

if [ "$HOUR" -lt 12 ]; then
    GREETING="Good morning"
elif [ "$HOUR" -lt 18 ]; then
    GREETING="Good afternoon"
else
    GREETING="Good evening"
fi

echo "$GREETING, $NAME! It is $(date '+%H:%M') right now."
echo "You are running this on: $(hostname)"
```

Run it:
```bash
chmod +x greet.sh
./greet.sh
```

---

### Exercise 2 — Number Guessing Game

```bash
#!/bin/bash
# guess.sh — Simple number guessing game

SECRET=$((RANDOM % 10 + 1))    # Random number 1-10
ATTEMPTS=0
MAX_ATTEMPTS=3

echo "🎮 Guess the number (between 1 and 10)"
echo "You have $MAX_ATTEMPTS attempts!"

while [ $ATTEMPTS -lt $MAX_ATTEMPTS ]; do
    read -p "Your guess: " GUESS
    (( ATTEMPTS++ ))

    if [ "$GUESS" -eq "$SECRET" ]; then
        echo "🎉 Correct! You got it in $ATTEMPTS attempt(s)!"
        exit 0
    elif [ "$GUESS" -lt "$SECRET" ]; then
        echo "📈 Too low! ($((MAX_ATTEMPTS - ATTEMPTS)) attempts left)"
    else
        echo "📉 Too high! ($((MAX_ATTEMPTS - ATTEMPTS)) attempts left)"
    fi
done

echo "😢 Game over! The number was $SECRET."
```

---

### Exercise 3 — File Organizer

```bash
#!/bin/bash
# organize.sh — Sort files by extension into folders

echo "📂 Organizing files in: $(pwd)"
COUNT=0

for FILE in *; do
    # Skip directories
    [ -d "$FILE" ] && continue
    [ "$FILE" = "organize.sh" ] && continue

    # Get the file extension
    EXT="${FILE##*.}"

    # Create a folder for this extension if it doesn't exist
    mkdir -p "$EXT"

    # Move the file
    mv "$FILE" "$EXT/"
    echo "  Moved: $FILE → $EXT/"
    (( COUNT++ ))
done

echo "✅ Done! Organized $COUNT file(s)."
```

---

### Exercise 4 — Simple Calculator

```bash
#!/bin/bash
# calc.sh — Interactive calculator

calculate() {
    local A=$1
    local OP=$2
    local B=$3

    case $OP in
        +) echo $(( A + B )) ;;
        -) echo $(( A - B )) ;;
        x) echo $(( A * B )) ;;
        /) 
            if [ "$B" -eq 0 ]; then
                echo "Error: Division by zero"
            else
                echo "scale=2; $A / $B" | bc
            fi
            ;;
        *) echo "Unknown operator: $OP" ;;
    esac
}

echo "🧮 Simple Calculator"
echo "Operators: + - x /"

while true; do
    read -p "Enter: number operator number (or 'q' to quit): " A OP B

    [ "$A" = "q" ] && break

    RESULT=$(calculate "$A" "$OP" "$B")
    echo "  Result: $A $OP $B = $RESULT"
    echo ""
done

echo "Goodbye!"
```

---

### Exercise 5 — Mini System Report

```bash
#!/bin/bash
# report.sh — Generate a system report and save it to a file

OUTPUT_FILE="system_report_$(date +%Y%m%d_%H%M%S).txt"

{
    echo "=================================="
    echo "   SYSTEM REPORT"
    echo "   Generated: $(date)"
    echo "=================================="
    echo ""
    echo "--- Basic Info ---"
    echo "Hostname : $(hostname)"
    echo "User     : $(whoami)"
    echo "OS       : $(uname -o)"
    echo "Kernel   : $(uname -r)"
    echo "Uptime   : $(uptime -p)"
    echo ""
    echo "--- Disk Usage ---"
    df -h
    echo ""
    echo "--- Memory ---"
    free -h
    echo ""
    echo "--- Top 5 Processes ---"
    ps aux --sort=-%cpu | head -6
    echo ""
    echo "--- Network Interfaces ---"
    ip -4 a | grep inet
    echo ""
    echo "=================================="
} > "$OUTPUT_FILE"

echo "✅ Report saved to: $OUTPUT_FILE"
cat "$OUTPUT_FILE"
```

---

## 📝 Summary

Today you learned:

| Concept | Syntax / Command |
|---------|-----------------|
| Shebang | `#!/bin/bash` |
| Comment | `# This is a comment` |
| Variable | `NAME="Alice"` |
| Use variable | `echo "$NAME"` or `echo "${NAME}"` |
| Read input | `read -p "Prompt: " VAR` |
| If statement | `if [ condition ]; then ... fi` |
| If/elif/else | `if ... elif ... else ... fi` |
| Numeric compare | `-eq -ne -lt -le -gt -ge` |
| String compare | `= != -z -n` |
| File test | `-f -d -e -r -w -x` |
| For loop | `for i in list; do ... done` |
| C-style for | `for (( i=1; i<=5; i++ )); do` |
| While loop | `while [ condition ]; do ... done` |
| Read file lines | `while IFS= read -r line; do` |
| Function | `my_func() { ... }` |
| Local variable | `local VAR="value"` |
| Array | `ARR=("a" "b" "c")` |
| Array access | `${ARR[0]}` `${ARR[@]}` |
| String length | `${#VAR}` |
| Substring | `${VAR:start:length}` |
| Replace string | `${VAR//old/new}` |
| Exit code | `$?` |
| Exit script | `exit 0` (success) `exit 1` (failure) |
| Strict mode | `set -euo pipefail` |
| Command substitution | `VAR=$(command)` |
| Arithmetic | `$(( A + B ))` |
| Float math | `echo "scale=2; 10/3" \| bc` |
| Cron schedule | `crontab -e` |
| Make executable | `chmod +x script.sh` |
| Run script | `./script.sh` |

### The Bash Script Template

```bash
#!/bin/bash
# script_name.sh — Description
# Author: Your Name
# Date: 2026-05-14

set -euo pipefail   # Strict mode

# ─── Variables ───────────────────────────
LOG_FILE="script.log"

# ─── Functions ───────────────────────────
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

usage() {
    echo "Usage: $0 <argument>"
    exit 1
}

# ─── Validate Input ──────────────────────
if [ "$#" -lt 1 ]; then
    usage
fi

# ─── Main Logic ──────────────────────────
log "Script started"

# Your code here...

log "Script completed"
exit 0
```

---

## 🎉 You Did It — What's Next?

You have completed the **7-Day Linux Learning Plan**! Here's what you've mastered:

| Day | Achievement |
|-----|------------|
| ✅ Day 1 | Installed Linux, opened the terminal, ran first commands |
| ✅ Day 2 | Created, copied, moved, and deleted files |
| ✅ Day 3 | Searched files with grep and find, mastered pipes |
| ✅ Day 4 | Managed users, permissions, chmod, sudo |
| ✅ Day 5 | Monitored processes, managed packages |
| ✅ Day 6 | Connected via SSH, transferred files, understood networking |
| ✅ Day 7 | Wrote bash scripts with loops, functions, and automation |

### Continue Your Linux Journey

```
Beginner (You are here!) ──────────────────────────────────────►
         │
         ├── Vim / Nano        → Master terminal text editors
         ├── systemd           → Manage services (start/stop/enable)
         ├── cron + scripts    → Full automation workflows
         ├── Docker            → Containerize applications
         ├── Git on Linux      → Version control from the terminal
         │
         ▼
Intermediate
         │
         ├── Networking deep dive (iptables, DNS server, VPN)
         ├── Web server setup (nginx, Apache)
         ├── Database admin (PostgreSQL, MySQL on Linux)
         ├── Python/Go scripting beyond bash
         │
         ▼
Advanced
         │
         ├── Linux Kernel basics
         ├── Security hardening
         ├── DevOps & CI/CD pipelines
         └── Kubernetes on Linux
```

### Recommended Next Steps

| Resource | What You'll Learn |
|----------|------------------|
| [Linux Journey](https://linuxjourney.com) | Deeper dives into every topic |
| [OverTheWire: Bandit](https://overthewire.org/wargames/bandit) | Linux skills through security challenges |
| [The Linux Command Line (free book)](https://linuxcommand.org/tlcl.php) | Comprehensive reference |
| [TryHackMe](https://tryhackme.com) | Hands-on Linux labs |
| [r/linuxquestions](https://reddit.com/r/linuxquestions) | Community help |

---

> 💬 **Stuck on a script?** Run `bash -x script.sh` to see each line as it executes — the best debugging tool!
>
> 🌐 **Community:** https://unix.stackexchange.com

---

<div align="center">

**🎓 7-Day Linux Learning Plan — COMPLETE!**

You went from zero to writing real automation scripts in 7 days.

⭐ Star this repository if this plan helped you!

[← Day 6](./Day6_Networking_and_SSH.md) | [Back to Learning Plan](./LEARNING_PLAN.md)

</div>
