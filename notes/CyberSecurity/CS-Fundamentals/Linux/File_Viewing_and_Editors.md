## 1. Introduction

Before you can process or edit text (see [[Text_Processing_Commands]]), you need to be able to view it — and eventually edit it directly. This note covers the standard file-viewing commands and the two dominant terminal text editors every Linux user/administrator encounters: **vim** and **nano**.

## 2. Viewing Entire Files

```bash
cat file.txt              # print the whole file to the terminal
cat -n file.txt             # with line numbers
tac file.txt                  # print in reverse line order ("cat" backwards)
```

`cat` is best for short files — for anything long, use a pager (below) instead of flooding the terminal.

## 3. Paging Through Files — less and more

| Command | Notes |
|---|---|
| `less file.txt` | Modern standard pager — scroll forward AND backward, search, doesn't load the whole file into memory upfront |
| `more file.txt` | Older, more limited pager — forward-only scrolling in its basic form |

**Key bindings inside `less`:**

| Key | Action |
|---|---|
| `Space` / `f` | Next page |
| `b` | Previous page |
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` / `N` | Next / previous search match |
| `g` / `G` | Go to start / end of file |
| `q` | Quit |

```bash
less /var/log/syslog
journalctl -u sshd | less        # pipe command output into less to page through it
```

## 4. Viewing Parts of a File — head and tail

```bash
head file.txt              # first 10 lines (default)
head -n 20 file.txt          # first 20 lines
tail file.txt                # last 10 lines
tail -n 50 file.txt            # last 50 lines
tail -f /var/log/syslog          # follow — continuously show new lines as they're appended (essential for live log monitoring)
tail -f access.log | grep "ERROR"  # follow and filter in real time
```

`tail -f` is one of the most-used commands in live security monitoring and troubleshooting — watching a log file update in real time as events occur.

## 5. vim — The Modal Text Editor

**vim** (Vi IMproved) is a modal editor — different modes interpret keystrokes differently, which is the source of most beginner confusion but enables extremely fast editing once learned. Present on nearly every Linux system by default (or its predecessor `vi`).

### Modes

| Mode | Purpose | Enter With |
|---|---|---|
| Normal (Command) | Navigate, delete, copy, run commands — the default mode | `Esc` |
| Insert | Type text directly | `i`, `a`, `o` (from Normal mode) |
| Visual | Select text for operations | `v` (character), `V` (line), `Ctrl+v` (block) |
| Command-line | Save, quit, search/replace, execute ex commands | `:` (from Normal mode) |

### Essential Commands

```bash
vim file.txt          # open a file

# Normal mode navigation
h j k l                # left, down, up, right
w / b                    # jump forward/backward by word
0 / $                     # start / end of line
gg / G                     # start / end of file
:n                          # go to line n

# Entering Insert mode
i                       # insert before cursor
a                        # insert after cursor
o                         # open a new line below and insert

# Editing (Normal mode)
x                        # delete character
dd                        # delete (cut) current line
yy                         # yank (copy) current line
p                            # paste after cursor
u                              # undo
Ctrl+r                          # redo

# Saving and quitting (Command-line mode)
:w                        # save
:q                         # quit
:wq  or  :x                 # save and quit
:q!                          # quit without saving (discard changes)
```

### Search and Replace in vim

```bash
:/pattern              # search forward
:%s/old/new/g            # replace all occurrences in the entire file
:%s/old/new/gc             # replace with confirmation prompt per match
```

## 6. nano — The Beginner-Friendly Editor

**nano** is a simpler, non-modal editor — you type directly, no mode-switching required, with on-screen key hints, making it the common recommendation for newcomers.

```bash
nano file.txt

# Common shortcuts (shown at the bottom of the screen, ^ means Ctrl)
Ctrl+O    # write out (save)
Ctrl+X    # exit
Ctrl+K    # cut line
Ctrl+U    # paste (uncut)
Ctrl+W    # search
Ctrl+_    # go to line number
```

## 7. vim vs nano — When to Use Which

| Aspect | vim | nano |
|---|---|---|
| Learning curve | Steep (modal editing) | Minimal |
| Editing speed once learned | Very fast, keyboard-only workflows | Slower for complex edits |
| Availability | Present (or as `vi`) on virtually every Unix/Linux system, even minimal installs | Usually present, but not guaranteed on stripped-down/embedded systems |
| Best for | Power users, frequent server admin, scripting on remote systems | Quick edits, beginners, one-off config changes |

**Practical reality:** because `vi`/`vim` is essentially guaranteed to exist on any Unix-like system (even a minimal container or embedded device), it's worth knowing at least the basics — `i` to insert, `Esc` to exit insert mode, `:wq` to save and quit — even if you prefer another editor day-to-day.

## 8. Comparing Files While Editing

```bash
vimdiff file1 file2      # side-by-side diff view within vim
```

## 9. Security/Forensic Relevance

- `tail -f` on log files is a core **live monitoring** technique during incident response.
- `less`/`head`/`tail` avoid loading huge files entirely into memory — important when inspecting massive log files or forensic artifacts without crashing a constrained analysis environment.
- Editing config files (`sshd_config`, firewall rules, etc.) during hardening exercises is almost always done via `vim` or `nano` directly on the target system.
- `cat`/`less` on binary files can produce garbled terminal output or even trigger terminal escape sequence issues — use `file` first (see [[Essential_Commands]]) to check before viewing unknown files.

## 10. Interview Questions

1. What's the key conceptual difference between vim and nano? → **vim is modal (different modes for navigation vs typing); nano is non-modal, type-directly editing**
2. How do you save and quit in vim? → **`:wq` (or `:x`) in Command-line mode**
3. Why is `tail -f` particularly useful for security monitoring? → **It continuously displays new lines appended to a log file in real time, ideal for watching live activity**
4. What's the advantage of `less` over `cat` for viewing a large file? → **`less` pages through content without loading the entire file into memory and allows searching/scrolling both directions**
5. How would you replace all instances of a word in an entire file using vim? → **`:%s/old/new/g`**
6. Why should you check a file's type with `file` before viewing an unknown file with `cat`? → **Binary content displayed to the terminal can produce garbled output or unexpected terminal behavior**

## 11. Key Points

- **cat** for short files; **less** (preferred) or **more** for paging through longer ones.
- **head**/**tail** view the start/end of a file; **`tail -f`** is essential for live log monitoring.
- **vim** is modal (Normal/Insert/Visual/Command-line) — steep learning curve but powerful and nearly universally available.
- **nano** is simpler and beginner-friendly, with on-screen shortcuts, but less universally guaranteed on minimal systems.
- Knowing at least basic vim (`i`, `Esc`, `:wq`) is a practical necessity since it's present on almost every Linux/Unix system.

---
*Related: [[Essential_Commands]], [[Text_Processing_Commands]], [[Log_Management]]*
