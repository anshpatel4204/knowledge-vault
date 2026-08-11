## 1. Introduction

**Environment variables** hold configuration data available to processes at runtime — where to find executables, a user's home directory, locale settings, and countless application-specific settings. Understanding how they're set, scoped, and loaded (and why the same command can "work in my terminal but not in a script") is essential for both scripting and general troubleshooting.

## 2. Viewing Environment Variables

```bash
env                   # list all environment variables
printenv                 # equivalent, alternative syntax
echo $PATH                 # view a specific variable
printenv PATH                # equivalent
set                             # list ALL variables (environment + shell-local), plus functions
```

## 3. Setting Variables — Shell vs Environment

```bash
myvar="hello"           # shell variable — only visible in the CURRENT shell, not to child processes
export myvar               # promote it to an environment variable — now inherited by child processes
export myvar2="world"         # set and export in one step
unset myvar                     # remove a variable entirely
```

**Key distinction:** a plain shell variable is local to that shell instance; `export` makes it part of the environment passed down to any child process (scripts, programs) that shell launches. This explains a common confusion — a variable set in a script without `export` won't be visible to any external commands that script calls.

## 4. Common Built-In Environment Variables

| Variable | Meaning |
|---|---|
| `$PATH` | Colon-separated list of directories searched for executable commands |
| `$HOME` | Current user's home directory |
| `$USER` | Current username |
| `$SHELL` | Path to the user's default shell |
| `$PWD` | Current working directory |
| `$OLDPWD` | Previous working directory (used by `cd -`) |
| `$LANG` / `$LC_*` | Locale/language settings |
| `$TERM` | Terminal type, affects how programs render output |
| `$EDITOR` | Default text editor invoked by other programs (e.g., `crontab -e`, `visudo`) |

## 5. $PATH — How the Shell Finds Commands

```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

When you type a command, the shell searches each directory in `$PATH`, **left to right**, and runs the first matching executable it finds.

```bash
export PATH="$HOME/bin:$PATH"       # prepend a custom directory (checked FIRST)
export PATH="$PATH:/opt/tool/bin"      # append a custom directory (checked LAST)
```

**Security relevance — PATH hijacking:** if an attacker can write to any directory earlier in `$PATH` than the legitimate binary's location (or if `.` — the current directory — is carelessly included early in PATH), they can plant a malicious file with the same name as a common command, which then executes instead of the real one — a classic privilege escalation technique (see [[Linux_Privilege_Escalation]]).

```bash
which ls          # confirm exactly which binary would actually run
type ls             # also shows if it's a builtin, alias, or the resolved binary path
```

## 6. Shell Configuration Files — Load Order

See [[Linux_Shell_Basics]] for the login vs non-login / interactive vs non-interactive distinction this all builds on.

| File | Scope | When Read |
|---|---|---|
| `/etc/environment` | System-wide | Set by PAM at login, not shell-specific, simple `KEY=value` format only |
| `/etc/profile` | System-wide | Login shells |
| `/etc/profile.d/*.sh` | System-wide | Sourced by `/etc/profile`, per-application settings |
| `/etc/bash.bashrc` | System-wide | Non-login interactive shells (Debian-style) |
| `~/.bash_profile` or `~/.profile` | Per-user | Login shells |
| `~/.bashrc` | Per-user | Non-login interactive shells |
| `~/.bash_logout` | Per-user | Executed when a login shell exits |

**Typical practical setup:** `~/.bash_profile` sources `~/.bashrc` (so login shells get the same aliases/settings as regular new terminal windows), and `~/.bashrc` is where users actually put their custom `export PATH=...`, aliases, and prompt customizations.

## 7. Aliases

```bash
alias ll='ls -la'
alias grep='grep --color=auto'
unalias ll
```

Aliases are typically defined in `~/.bashrc` so they persist across new interactive shell sessions. They only apply to **interactive** shell use — scripts generally shouldn't rely on aliases, since non-interactive shells (like those running scripts) don't expand them by default.

## 8. Custom Prompt (PS1)

```bash
echo $PS1                  # view current prompt format
export PS1='\u@\h:\w\$ '     # username@host:working_directory$
```

| Escape | Meaning |
|---|---|
| `\u` | Username |
| `\h` | Hostname (short) |
| `\w` | Current working directory (full path) |
| `\W` | Current directory (basename only) |
| `\$` | `$` for regular users, `#` for root |

## 9. Environment Variables and Cron/Services — A Common Pitfall

Cron jobs (see [[Cron_and_Task_Scheduling]]) and systemd services (see [[Systemd_and_Services]]) run in a **minimal environment**, not sourcing `~/.bashrc` or `~/.bash_profile` at all — this is exactly why scripts that work fine interactively can fail mysteriously when run via cron/systemd (missing PATH entries, missing custom variables).

**Fix:** always use absolute paths in scripts intended for cron/systemd, or explicitly set required environment variables at the top of the script/crontab rather than relying on interactive shell config being loaded.

## 10. Persisting Variables System-Wide

```bash
# /etc/environment (system-wide, simple KEY=value, no shell syntax/expansion)
JAVA_HOME=/usr/lib/jvm/java-17

# /etc/profile.d/myapp.sh (system-wide, full shell syntax allowed, sourced at login)
export MYAPP_CONFIG=/etc/myapp/config.yaml
```

## 11. Interview Questions

1. What's the difference between a shell variable and an environment variable? → **A shell variable is local to the current shell; `export` promotes it to an environment variable, inherited by child processes**
2. How does the shell decide which binary runs when you type a command name? → **It searches each directory listed in `$PATH`, left to right, using the first match found**
3. Why might a script behave differently when run via cron compared to running it manually in a terminal? → **Cron runs in a minimal environment that doesn't source `~/.bashrc`/`~/.bash_profile`, so PATH and custom variables may be missing**
4. What is PATH hijacking, and how could it lead to privilege escalation? → **Placing a malicious executable in a directory that appears earlier in $PATH than the legitimate one, so it runs instead when a victim (potentially a privileged script) calls that command name**
5. What's the difference between `~/.bash_profile` and `~/.bashrc`? → **`.bash_profile` is read by login shells; `.bashrc` is read by non-login interactive shells — typically one sources the other for consistency**
6. Why don't aliases typically work inside non-interactive shell scripts? → **Aliases are an interactive-shell feature and aren't expanded by default in non-interactive script execution**

## 12. Key Points

- **`export`** promotes a shell-local variable into the environment, making it inheritable by child processes.
- **`$PATH`** determines where the shell looks for commands — order matters, and a misconfigured/writable PATH entry is a real **privilege escalation** vector.
- Config file loading depends on **login vs non-login** and **interactive vs non-interactive** shell type — a frequent source of "works in my terminal, not in my script/cron job" bugs.
- **Cron and systemd services run in minimal environments** — always use absolute paths and explicit variable definitions in automation.
- `~/.bashrc` (interactive) and `~/.bash_profile`/`~/.profile` (login) are the primary per-user customization points.

---
*Related: [[Linux_Shell_Basics]], [[Cron_and_Task_Scheduling]], [[Linux_Privilege_Escalation]], [[Shell_Scripting]]*
