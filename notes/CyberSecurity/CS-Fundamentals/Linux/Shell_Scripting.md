## 1. Introduction

**Shell scripting** automates sequences of commands by writing them into an executable text file, rather than typing them interactively one at a time. Bash scripting is the standard for Linux automation, system administration tasks, and — heavily — for writing custom security tooling, enumeration scripts, and CTF automation.

## 2. The Shebang Line

Every script should start with a **shebang** telling the system which interpreter to use:

```bash
#!/bin/bash
```

```bash
chmod +x script.sh      # make it executable (see [[File_Permissions]])
./script.sh                # run it
bash script.sh               # alternative — run explicitly with bash regardless of the shebang/executable bit
```

## 3. Variables

```bash
name="Ansh"
echo "Hello, $name"
echo "Hello, ${name}"        # braces avoid ambiguity when concatenating (e.g., "${name}_suffix")

readonly PI=3.14              # constant, cannot be reassigned
unset name                      # remove a variable
```

**No spaces around `=`** — `name = "Ansh"` is a syntax error in bash (it's interpreted as running a command named `name` with arguments).

## 4. Special Variables

| Variable | Meaning |
|---|---|
| `$0` | Script name |
| `$1`, `$2`, ... | Positional arguments passed to the script |
| `$#` | Number of arguments passed |
| `$@` | All arguments as separate words |
| `$*` | All arguments as a single string |
| `$?` | Exit status of the last command |
| `$$` | PID of the current script/shell |
| `$USER`, `$HOME`, `$PATH` | Environment variables (see [[Environment_Variables_and_Shell_Config]]) |

```bash
#!/bin/bash
echo "Script name: $0"
echo "First argument: $1"
echo "Total arguments: $#"
```

## 5. Reading User Input

```bash
read -p "Enter your name: " name
echo "Hello, $name"
```

## 6. Conditionals — if/elif/else

```bash
if [ "$1" == "start" ]; then
    echo "Starting..."
elif [ "$1" == "stop" ]; then
    echo "Stopping..."
else
    echo "Unknown command"
fi
```

**Test operators** (used inside `[ ]` or the more modern `[[ ]]`):

| Category | Operators |
|---|---|
| String | `==`, `!=`, `-z` (empty), `-n` (not empty) |
| Numeric | `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le` |
| File | `-f` (regular file exists), `-d` (directory exists), `-x` (executable), `-r` (readable), `-w` (writable) |
| Logical | `&&`, `\|\|`, `!` |

```bash
if [ -f "/etc/passwd" ]; then echo "File exists"; fi
if [ "$count" -gt 10 ]; then echo "Over 10"; fi
if [[ "$name" == "admin" && -x "$0" ]]; then echo "Admin and executable"; fi
```

**`[[ ]]` vs `[ ]`:** `[[ ]]` is a bash-specific enhancement supporting pattern matching, regex (`=~`), and avoiding some word-splitting/quoting pitfalls that plague `[ ]` (which is actually the POSIX `test` command in disguise).

## 7. Loops

### for loop

```bash
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

for file in *.txt; do
    echo "Processing $file"
done

for i in $(seq 1 10); do echo $i; done
```

### while loop

```bash
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    count=$((count + 1))
done

# Reading a file line by line
while read -r line; do
    echo "Line: $line"
done < input.txt
```

### until loop

```bash
count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    count=$((count + 1))
done
```

## 8. Case Statements

```bash
case "$1" in
    start)
        echo "Starting service"
        ;;
    stop)
        echo "Stopping service"
        ;;
    restart)
        echo "Restarting service"
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        ;;
esac
```

## 9. Functions

```bash
greet() {
    local name=$1          # 'local' scopes the variable to this function only
    echo "Hello, $name"
}

greet "Ansh"

# Return values via exit status (0-255 only) or by echoing and capturing output
is_even() {
    if [ $(($1 % 2)) -eq 0 ]; then
        return 0    # success/true
    else
        return 1    # failure/false
    fi
}

if is_even 4; then echo "Even"; fi
```

## 10. Arithmetic

```bash
result=$((5 + 3))
echo $result

let count++
count=$((count + 1))

# Floating point requires external tools (bash only does integer arithmetic)
echo "3.5 + 2.1" | bc
```

## 11. Arrays

```bash
fruits=("apple" "banana" "cherry")
echo "${fruits[0]}"          # first element
echo "${fruits[@]}"           # all elements
echo "${#fruits[@]}"            # array length

for fruit in "${fruits[@]}"; do
    echo "$fruit"
done
```

## 12. Exit Codes and Error Handling

```bash
exit 0            # success
exit 1              # generic error

set -e             # exit immediately if any command fails (useful for safer scripts)
set -u              # treat unset variables as an error
set -x               # print each command before executing it (debugging)
set -euo pipefail      # common strict-mode combination for robust scripts
```

## 13. Practical Security-Oriented Example

```bash
#!/bin/bash
# Simple enumeration script: find SUID binaries and world-writable files

echo "=== SUID Binaries ==="
find / -perm -4000 -type f 2>/dev/null

echo "=== World-Writable Files ==="
find / -perm -0002 -type f 2>/dev/null

echo "=== Listening Ports ==="
ss -tulnp 2>/dev/null

echo "=== Sudo Permissions for Current User ==="
sudo -l 2>/dev/null
```

## 14. Interview Questions

1. What line must start a bash script, and what does it do? → **The shebang (`#!/bin/bash`) — tells the OS which interpreter to run the script with**
2. What's the difference between `$@` and `$*`? → **`$@` treats arguments as separate words; `$*` treats them as one combined string**
3. What does `set -e` do in a script? → **Causes the script to exit immediately if any command returns a non-zero exit status**
4. What's the difference between `[ ]` and `[[ ]]` in bash conditionals? → **`[[ ]]` is a bash-specific enhancement supporting regex matching and avoiding some quoting/word-splitting issues present in POSIX `[ ]`**
5. How do you make a script executable and then run it? → **`chmod +x script.sh` then `./script.sh`**
6. How would you loop through all `.txt` files in a directory? → **`for file in *.txt; do ... done`**

## 15. Key Points

- Scripts start with a **shebang** (`#!/bin/bash`) and need execute permission (`chmod +x`) to run directly.
- Variables: no spaces around `=`; special variables (`$1`, `$#`, `$?`, `$@`) handle arguments and status.
- Conditionals use `[ ]`/`[[ ]]`/`test`; loops come in `for`/`while`/`until` forms.
- **`set -euo pipefail`** is a common strict-mode combination for writing safer, more predictable scripts.
- Shell scripting is heavily used for **security automation** — enumeration scripts, log parsing pipelines, and CTF tooling all build on these fundamentals.

---
*Related: [[Linux_Shell_Basics]], [[Piping_Redirection_and_Operators]], [[Regular_Expressions]], [[Cron_and_Task_Scheduling]]*
