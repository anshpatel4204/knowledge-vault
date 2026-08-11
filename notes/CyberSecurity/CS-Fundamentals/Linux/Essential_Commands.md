## 1. Introduction

This note catalogs the core command-line tools used constantly for navigation, file management, and system exploration in Linux — the baseline vocabulary needed before anything else (scripting, security tooling, log analysis) makes sense.

## 2. Navigation Commands

| Command | Purpose |
|---|---|
| `pwd` | Print working (current) directory |
| `cd <dir>` | Change directory |
| `cd ..` | Move up one directory |
| `cd ~` or `cd` | Go to home directory |
| `cd -` | Go to the previous directory |
| `ls` | List directory contents |
| `ls -l` | Long format (permissions, owner, size, date) |
| `ls -a` | Include hidden files (starting with `.`) |
| `ls -lh` | Long format with human-readable sizes (K, M, G) |
| `ls -R` | Recursive listing |
| `tree` | Display directory structure as a tree (may need installing) |

## 3. File and Directory Creation/Removal

| Command | Purpose |
|---|---|
| `touch file.txt` | Create an empty file, or update its timestamp if it exists |
| `mkdir dir` | Create a directory |
| `mkdir -p a/b/c` | Create nested directories, creating parents as needed |
| `rm file.txt` | Remove a file |
| `rm -r dir` | Remove a directory and its contents recursively |
| `rm -rf dir` | Force recursive removal, no confirmation — **dangerous, use carefully** |
| `rmdir dir` | Remove an *empty* directory only |

## 4. Copying, Moving, and Renaming

| Command | Purpose |
|---|---|
| `cp source dest` | Copy a file |
| `cp -r sourcedir destdir` | Copy a directory recursively |
| `cp -p source dest` | Copy while preserving permissions/timestamps |
| `mv source dest` | Move (or rename, if dest is a new filename in the same location) |

```bash
cp report.txt report_backup.txt
mv oldname.txt newname.txt          # renaming is just "moving" within the same directory
mv file.txt /home/ansh/documents/
```

## 5. Finding Files

| Command | Purpose |
|---|---|
| `find <path> -name "pattern"` | Search for files by name |
| `find / -type f -size +100M` | Find files over 100MB |
| `find / -perm -4000` | Find SUID files (see [[Linux_Privilege_Escalation]]) |
| `find / -mtime -1` | Find files modified in the last day |
| `find / -user ansh` | Find files owned by a specific user |
| `locate filename` | Fast search using a prebuilt index (`updatedb` to refresh) |
| `which command` | Show the path of an executable found in `$PATH` |
| `whereis command` | Show binary, source, and man page locations |

```bash
find / -name "*.conf" 2>/dev/null
find /var/log -mtime -7 -name "*.log"    # logs modified in the last week
```

## 6. Viewing File/Directory Information

| Command | Purpose |
|---|---|
| `stat file` | Detailed metadata (inode, permissions, timestamps — see [[File_Types_and_Inodes]]) |
| `file file` | Determine actual file type by content, not extension |
| `du -sh dir` | Disk usage of a directory (summarized, human-readable) |
| `wc -l file` | Count lines in a file |

## 7. Working with Links

```bash
ln source hardlink        # create a hard link
ln -s source symlink       # create a symbolic link
```

See [[File_Types_and_Inodes]] for the full hard link vs symlink distinction.

## 8. Comparing Files

| Command | Purpose |
|---|---|
| `diff file1 file2` | Show line-by-line differences |
| `diff -u file1 file2` | Unified diff format (used in patches) |
| `cmp file1 file2` | Byte-by-byte comparison |
| `md5sum file` / `sha256sum file` | Compute a hash for integrity verification |

```bash
sha256sum download.iso
# compare against a published checksum to verify file integrity
```

## 9. Working with Multiple Files — Wildcards Recap

See [[Linux_Shell_Basics]] for the full wildcard/globbing reference — used constantly with these commands (`rm *.tmp`, `cp *.jpg backup/`, etc.).

## 10. Getting Help

| Command | Purpose |
|---|---|
| `man command` | Full manual page |
| `command --help` | Quick usage summary |
| `whatis command` | One-line description |
| `info command` | GNU's alternative documentation system (sometimes more detailed than man) |
| `apropos keyword` | Search man page descriptions for a keyword |

```bash
man ls
man -k network        # equivalent to apropos, search man pages
```

## 11. Command Chaining Quick Reference (Detail in [[Piping_Redirection_and_Operators]])

```bash
cmd1 | cmd2        # pipe output of cmd1 into cmd2
cmd1 && cmd2        # run cmd2 only if cmd1 succeeds
cmd1 ; cmd2          # run both regardless of success
```

## 12. Practical Examples

```bash
# Find and delete all .tmp files older than 7 days
find /tmp -name "*.tmp" -mtime +7 -delete

# Count how many .log files exist in /var/log
ls /var/log/*.log | wc -l

# Recursively copy a directory, preserving permissions
cp -rp /etc/nginx /backup/nginx_config

# Find all world-writable files (security audit)
find / -perm -0002 -type f 2>/dev/null
```

## 13. Interview Questions

1. What's the difference between `rm -r` and `rmdir`? → **`rmdir` only removes empty directories; `rm -r` recursively removes a directory and everything inside it**
2. How would you find all files modified in the last 24 hours under `/var/log`? → **`find /var/log -mtime -1`**
3. What's the difference between `locate` and `find`? → **`locate` uses a prebuilt index (fast but can be stale); `find` searches live in real time (slower but always current)**
4. Why is `file document.txt` more trustworthy than trusting the `.txt` extension? → **`file` inspects actual content/magic bytes; Linux doesn't rely on extensions to determine file type**
5. What command computes a file's SHA-256 hash for integrity verification? → **`sha256sum`**
6. How would you find all SUID binaries on the system? → **`find / -perm -4000 -type f 2>/dev/null`**

## 14. Key Points

- Navigation: `pwd`, `cd`, `ls` (with `-l`, `-a`, `-h`, `-R` flags) form the daily-use baseline.
- File ops: `touch`, `mkdir`, `cp`, `mv`, `rm` — always double-check before `rm -rf`.
- `find` is the powerhouse search tool — critical for security audits (SUID files, world-writable files, recently modified files).
- `file`, `stat`, and checksum tools (`md5sum`/`sha256sum`) are essential for verifying what a file actually is and whether it's been tampered with.
- `man`, `--help`, and `apropos` are the built-in documentation system — always available, no internet required.

---
*Related: [[Linux_Shell_Basics]], [[File_Permissions]], [[Text_Processing_Commands]], [[Piping_Redirection_and_Operators]]*
