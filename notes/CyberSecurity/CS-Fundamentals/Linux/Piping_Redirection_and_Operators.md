## 1. Introduction

Linux's power as a command-line environment comes largely from its ability to **connect simple programs together** and **control where their input/output goes** — piping and redirection are exactly how the Unix philosophy of "small tools composed together" (see [[Linux_Basics]]) actually works in practice.

## 2. Standard Streams — The Foundation

Every process starts with three standard I/O streams, each identified by a **file descriptor number**:

| Stream | File Descriptor | Default Destination |
|---|---|---|
| stdin (standard input) | 0 | Keyboard input |
| stdout (standard output) | 1 | Terminal screen |
| stderr (standard error) | 2 | Terminal screen (separate from stdout, even though both display there by default) |

Redirection and piping work by **reassigning** where these streams read from or write to.

## 3. Output Redirection

```bash
command > file.txt          # redirect stdout to a file (OVERWRITES existing content)
command >> file.txt           # append stdout to a file (preserves existing content)
command 2> errors.txt           # redirect stderr only
command 2>> errors.txt            # append stderr only
command > out.txt 2>&1              # redirect BOTH stdout and stderr to the same file
command &> both.txt                   # shorthand for the above (bash-specific)
command > /dev/null 2>&1                # discard all output entirely
```

**Order matters** in `2>&1` constructs: `command > file 2>&1` redirects stdout to the file, then points stderr at "wherever stdout currently points" (the file) — reversing the order produces different (usually wrong) results.

## 4. Input Redirection

```bash
command < input.txt         # feed a file's contents as stdin to a command
command << EOF                # "here document" — feed multi-line inline text as stdin until EOF marker
line 1
line 2
EOF
command <<< "single line"      # "here string" — feed a single string as stdin
```

## 5. Piping — Connecting Commands

The pipe operator `|` sends one command's **stdout** directly into the next command's **stdin**, without needing an intermediate file.

```bash
ps aux | grep nginx                  # filter process list
cat access.log | grep "404" | wc -l    # count 404 errors in a log
ls -la | sort -k5 -n                    # sort files by size (5th column)
```

Chains of pipes can be arbitrarily long — this is the core mechanism behind the powerful one-liners shown in [[Text_Processing_Commands]].

## 6. Command Chaining / Control Operators

| Operator | Behavior |
|---|---|
| `;` | Run commands sequentially, regardless of success/failure of the prior one |
| `&&` | Run the next command **only if** the previous one succeeded (exit status 0) |
| `\|\|` | Run the next command **only if** the previous one failed (non-zero exit status) |
| `&` | Run the command in the **background**, immediately returning control of the shell |

```bash
mkdir newdir ; cd newdir                  # run both regardless
make && make install                        # only install if the build succeeded
ping -c1 google.com || echo "No internet"      # print message only if ping fails
long_running_task &                              # run in background, get prompt back immediately
```

## 7. Command Substitution

Captures a command's **output** and inserts it inline as a value/argument for another command.

```bash
echo "Today is $(date)"
echo "Today is `date`"          # older backtick syntax, functionally equivalent to $()
files=$(ls *.txt)
for f in $(find . -name "*.log"); do echo "$f"; done
```

`$(...)` is preferred over backticks in modern scripts — easier to nest and read.

## 8. Process Substitution (Bash-specific)

Treats a command's output as if it were a temporary file — useful when a tool expects a filename argument rather than piped input.

```bash
diff <(ls dir1) <(ls dir2)          # compare the output of two commands as if they were files
```

## 9. Grouping Commands

```bash
(cd /tmp && ls)          # runs in a subshell — the cd doesn't affect the parent shell's directory
{ echo "a"; echo "b"; } > output.txt     # runs in the current shell, output combined and redirected together
```

## 10. tee — Splitting Output to Multiple Destinations

`tee` reads from stdin and writes to **both** stdout and one or more files simultaneously — useful when you want to both see output live and save it.

```bash
command | tee output.txt                  # show output AND save to a file
command | tee -a output.txt                 # append instead of overwrite
command | tee file1.txt file2.txt             # write to multiple files at once
ping google.com | tee ping_log.txt              # watch live while logging
```

## 11. Practical Combined Examples

```bash
# Log all output (both stdout and stderr) of a script, while still seeing it live
./script.sh 2>&1 | tee script_output.log

# Only proceed with a backup if disk space check passes
df -h / | grep -q "9[0-9]%" && echo "Disk almost full!" || rsync -av /data /backup/

# Silently run a background job and don't clutter output
long_task > /dev/null 2>&1 &

# Feed a heredoc into a remote command over SSH
ssh user@host << 'EOF'
whoami
uname -a
EOF
```

## 12. Interview Questions

1. What's the difference between `>` and `>>`? → **`>` overwrites the destination file; `>>` appends to it**
2. What does `2>&1` do, and why does order matter? → **Redirects stderr to wherever stdout currently points; if written after stdout's own redirection, it correctly merges both into the same destination — reversed order does not**
3. What's the difference between `&&` and `;` when chaining commands? → **`&&` only runs the next command if the previous succeeded; `;` runs it regardless**
4. What does the pipe operator (`|`) actually connect? → **The stdout of one command to the stdin of the next**
5. What does `command 2>&1 | tee file.txt` accomplish? → **Merges stdout and stderr, then displays the combined output live while also saving it to a file**
6. What's command substitution, and give its modern syntax? → **Capturing a command's output for use as a value — `$(command)`, preferred over legacy backticks**

## 13. Key Points

- Three standard streams: **stdin (0), stdout (1), stderr (2)** — redirection reassigns where they read from/write to.
- `>` overwrites, `>>` appends; `2>&1` merges stderr into stdout's destination.
- **Pipes (`|`)** connect one command's stdout directly to the next command's stdin — the backbone of Unix-style command composition.
- **`&&`**, **`||`**, **`;`**, and **`&`** control sequencing and success-dependent execution.
- **`tee`** splits output to both the screen and a file simultaneously — useful for logging while observing live.

---
*Related: [[Linux_Shell_Basics]], [[Text_Processing_Commands]], [[Shell_Scripting]], [[Process_Management]]*
