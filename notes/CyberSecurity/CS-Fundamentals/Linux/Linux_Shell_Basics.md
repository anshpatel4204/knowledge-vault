## 1. Introduction

The **shell** is a user-space program that provides a command-line interface between the user and the operating system — it reads commands you type, interprets them, and requests the kernel perform the corresponding actions (see [[Linux_Architecture]]). It's the primary interface for nearly all Linux system administration and security work.

## 2. Shell vs Terminal vs Console — Clearing Up the Confusion

| Term | Definition |
|---|---|
| Shell | The program that interprets commands (bash, zsh, sh) |
| Terminal (Terminal Emulator) | The application window that provides an interface to interact with a shell (GNOME Terminal, iTerm2, Windows Terminal, PuTTY) |
| Console | Historically, the physical hardware terminal directly attached to a machine; today often used loosely to mean any terminal |
| TTY | The underlying kernel abstraction representing a terminal device (physical or virtual) |

In practice: you open a **terminal emulator**, which starts a **shell**, which is connected to a **TTY**.

## 3. Common Shells

| Shell | Full Name | Notes |
|---|---|---|
| sh | Bourne Shell | The original Unix shell (1977), many modern shells aim for compatibility with its scripting syntax |
| bash | Bourne Again Shell | The default on most Linux distributions, GNU project's enhanced sh replacement |
| dash | Debian Almquist Shell | Minimal, POSIX-compliant, faster than bash — often used as `/bin/sh` on Debian/Ubuntu for scripts, not as the interactive login shell |
| zsh | Z Shell | Highly configurable, popular for interactive use (auto-completion, plugins via frameworks like Oh My Zsh), macOS's default since Catalina |
| fish | Friendly Interactive Shell | User-friendly defaults, syntax highlighting, autosuggestions out of the box, less POSIX-compatible |
| ksh | Korn Shell | Influential historically, blends features of sh and csh |

```bash
echo $SHELL          # show your current default shell
cat /etc/shells       # list installed/allowed shells
chsh -s /bin/zsh       # change default shell
```

## 4. Login Shell vs Non-Login Shell, Interactive vs Non-Interactive

| Type | Description | Config Files Read |
|---|---|---|
| Login shell | Started as part of logging in (e.g., SSH session, TTY login) | `/etc/profile`, `~/.bash_profile` or `~/.profile` |
| Non-login shell | Started from within an existing session (e.g., opening a new terminal window) | `~/.bashrc` |
| Interactive | Presents a prompt, waits for user input | Reads interactive config files |
| Non-interactive | Runs a script/command without a prompt (e.g., a cron job or script execution) | Typically skips most interactive config |

This distinction explains a very common real-world confusion: "why does my environment variable work in one terminal but not in a cron job/script?" — different shell invocation types read different configuration files (see [[Environment_Variables_and_Shell_Config]]).

## 5. The Shell Prompt and Basic Anatomy of a Command

```bash
ansh@hostname:~/projects$ command -flag1 -flag2 argument1 argument2
```

| Component | Description |
|---|---|
| Command | The program/built-in to execute |
| Flags/Options | Modify command behavior (short `-v` or long `--verbose`) |
| Arguments | Input data the command operates on |

```bash
ls -la /home/ansh
   │  │   └── argument
   │  └────── flag (combined -l and -a)
   └───────── command
```

## 6. Command Types

| Type | Description | Example |
|---|---|---|
| Built-in | Implemented directly in the shell itself, no separate process | `cd`, `echo`, `export`, `alias` |
| External/Binary | A separate executable program the shell locates and runs | `ls`, `grep`, `cat` |
| Alias | A shortcut name mapped to a longer command | `alias ll='ls -la'` |
| Function | A user-defined shell function (see [[Shell_Scripting]]) | Custom-defined |

```bash
type ls           # shows whether a command is a builtin, alias, function, or external binary + its path
which ls           # shows the path of an external binary
```

## 7. Command History

```bash
history                  # show command history
!123                       # re-run history entry #123
!!                          # re-run the last command
Ctrl+R                     # reverse-search through history interactively
```

History is stored in a file like `~/.bash_history` — a notable **forensic artifact** revealing exactly what commands a user (or attacker who gained shell access) ran on a system.

## 8. Tab Completion and Wildcards (Globbing)

| Wildcard | Meaning |
|---|---|
| `*` | Matches any number of characters (including none) |
| `?` | Matches exactly one character |
| `[abc]` | Matches any one character in the set |
| `[a-z]` | Matches any one character in the range |
| `{a,b,c}` | Brace expansion — expands to each listed option |

```bash
ls *.txt              # all files ending in .txt
ls file?.txt           # file1.txt, fileA.txt, etc. (single character)
ls file[123].txt        # file1.txt, file2.txt, or file3.txt
cp file.{txt,bak}       # expands to file.txt and file.bak
```

Wildcards are a shell feature (expanded **before** the command runs), not something each individual command implements itself — this is why `echo *` prints matching filenames, not a literal asterisk.

## 9. Shell Configuration Files (Overview — see [[Environment_Variables_and_Shell_Config]] for detail)

| File | Scope | Purpose |
|---|---|---|
| `/etc/profile` | System-wide | Login shell environment setup |
| `/etc/bash.bashrc` | System-wide | Non-login interactive shell setup |
| `~/.bash_profile` / `~/.profile` | Per-user | Login shell setup |
| `~/.bashrc` | Per-user | Non-login interactive shell setup (aliases, prompt customization) |

## 10. Command Exit Status

Every command returns a numeric **exit status** upon completion — `0` for success, non-zero for various failure conditions — used constantly in scripting for conditional logic (see [[Shell_Scripting]]).

```bash
ls /nonexistent
echo $?          # shows the exit status of the last command (non-zero = failed)
```

## 11. Interview Questions

1. What's the difference between a shell and a terminal? → **The shell interprets commands; the terminal is the application providing the interface to that shell**
2. What's the default shell on most Linux distributions? → **bash**
3. What's the difference between a login shell and a non-login shell in terms of configuration? → **Login shells read `/etc/profile`/`~/.bash_profile`; non-login interactive shells read `~/.bashrc`**
4. What command shows whether something is a shell builtin or an external binary? → **`type`**
5. Why is `~/.bash_history` significant in a forensic investigation? → **It records the actual commands executed by a user (or attacker), providing a timeline of activity**
6. What does `$?` show after running a command? → **The exit status of the previously executed command (0 = success)**

## 12. Key Points

- The **shell** interprets commands; the **terminal** provides the interface; both differ from the underlying **TTY** device abstraction.
- **bash** is the most common default shell; **zsh**, **dash**, and **fish** are common alternatives.
- **Login vs non-login** and **interactive vs non-interactive** shell types determine which config files are read — a frequent source of "works in my terminal but not in my script/cron" confusion.
- Wildcards (`*`, `?`, `[...]`, `{...}`) are expanded by the shell itself before the command ever runs.
- `~/.bash_history` and command **exit status (`$?`)** are essential for both daily use and forensic/scripting purposes.

---
*Related: [[Linux_Architecture]], [[Essential_Commands]], [[Environment_Variables_and_Shell_Config]], [[Shell_Scripting]]*
