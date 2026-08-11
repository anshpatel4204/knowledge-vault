## 1. Introduction

A **process** is a running instance of a program. Linux is a multi-tasking OS, running many processes concurrently by rapidly scheduling CPU time among them. Managing processes — viewing, controlling, prioritizing, and terminating them — is a core daily skill and a critical part of incident response (identifying and killing malicious processes).

## 2. Viewing Processes — ps

```bash
ps                     # processes in the current shell session only
ps aux                  # ALL processes, all users, detailed (BSD-style syntax)
ps -ef                   # ALL processes, full format (System V-style syntax)
ps aux | grep nginx        # find a specific process
```

**Key `ps aux` columns:**

| Column | Meaning |
|---|---|
| USER | Owner of the process |
| PID | Process ID |
| %CPU | CPU usage percentage |
| %MEM | Memory usage percentage |
| VSZ / RSS | Virtual / physical memory size |
| STAT | Process state (see below) |
| START | Start time |
| COMMAND | The command that launched the process |

## 3. Process States

| State | Symbol | Meaning |
|---|---|---|
| Running | R | Currently executing or ready to run |
| Sleeping (interruptible) | S | Waiting for an event (most idle processes) |
| Sleeping (uninterruptible) | D | Waiting on I/O, cannot be interrupted (often disk operations) |
| Stopped | T | Suspended (e.g., via Ctrl+Z or SIGSTOP) |
| Zombie | Z | Finished executing, but its exit status hasn't been collected by its parent yet |

## 4. Real-Time Monitoring — top and htop

```bash
top          # real-time process viewer, updates periodically
htop          # improved, color, mouse-support, easier-to-read version (may need installing)
```

**Inside `top`:** press `P` to sort by CPU, `M` to sort by memory, `k` to kill a process, `q` to quit.

## 5. Process Identifiers

| Term | Meaning |
|---|---|
| PID | Process ID — unique identifier for a running process |
| PPID | Parent Process ID — the PID of the process that created it |
| PID 1 | Always the init system (systemd) — the ancestor of every other process (see [[Linux_Boot_Process]]) |

```bash
pstree                  # visualize the process tree (parent-child relationships)
ps -ef --forest            # ps with a tree-style indented view
```

## 6. Killing Processes — Signals

`kill` doesn't necessarily terminate a process directly — it sends a **signal**, and the process's own handling of that signal determines what actually happens.

| Signal | Number | Purpose |
|---|---|---|
| SIGHUP | 1 | Hang up — often used to tell a daemon to reload its config |
| SIGINT | 2 | Interrupt — same as pressing Ctrl+C |
| SIGKILL | 9 | Force-kill — cannot be caught, blocked, or ignored by the process |
| SIGTERM | 15 | Graceful termination request (the default signal `kill` sends) |
| SIGSTOP | 19 | Pause the process (cannot be caught/ignored) |
| SIGCONT | 18 | Resume a stopped process |

```bash
kill PID                  # send SIGTERM (graceful) by default
kill -9 PID                 # send SIGKILL (force, immediate)
kill -SIGHUP PID              # send a specific named signal
killall process_name            # kill by process name instead of PID
pkill -f "pattern"                # kill processes matching a command-line pattern
```

**SIGTERM vs SIGKILL — best practice:** always try `SIGTERM` (graceful, allows cleanup) before escalating to `SIGKILL` (immediate, no cleanup, can leave resources/locks in a bad state).

## 7. Foreground and Background Jobs

```bash
long_command &          # start in the background immediately
jobs                      # list background jobs in the current shell
fg %1                       # bring job 1 to the foreground
bg %1                        # resume a stopped job in the background
Ctrl+Z                         # suspend the current foreground process (SIGSTOP)
Ctrl+C                          # terminate the current foreground process (SIGINT)
```

## 8. Running Processes That Survive Logout

Normally, background jobs tied to a terminal session die when that session ends (via SIGHUP). To keep a process running after logout:

```bash
nohup long_command &          # ignores SIGHUP, output redirected to nohup.out by default
disown                          # detach an already-running job from the current shell's job table
setsid command                    # run in a completely new session, fully detached
screen                              # start a detachable terminal multiplexer session
tmux                                  # modern alternative to screen
```

`tmux`/`screen` are especially common on remote SSH sessions — allowing a long-running task to survive a dropped connection.

## 9. Process Priority — nice and renice

Linux schedules CPU time partly based on a process's **niceness** value, ranging from **-20 (highest priority) to 19 (lowest priority)**, default 0.

```bash
nice -n 10 command           # start a new process with lower priority (nicer to others)
renice -n 5 -p PID              # change the priority of an already-running process
renice -n -10 -p PID               # only root can set negative (higher-priority) values
```

## 10. Practical Examples

```bash
# Find and kill a process by name
pkill -9 -f "malicious_script.py"

# Find the top 5 CPU-consuming processes
ps aux --sort=-%cpu | head -6

# Find all processes owned by a specific user
ps -u ansh

# Check what a suspicious PID is doing (files it has open)
lsof -p 1234

# Find the parent of a suspicious process (useful in incident response)
ps -o ppid= -p 1234
```

## 11. Interview Questions

1. What's the difference between `SIGTERM` and `SIGKILL`? → **SIGTERM (15) requests graceful shutdown, which a process can catch/handle; SIGKILL (9) forcibly terminates immediately and cannot be caught or ignored**
2. What is a zombie process? → **A finished process whose exit status hasn't yet been collected by its parent, so its entry remains in the process table**
3. What does `nohup` do? → **Prevents a background process from being terminated by SIGHUP when the terminal session ends**
4. What's the difference between `fg`/`bg` and `Ctrl+Z`? → **Ctrl+Z suspends (SIGSTOP) the current foreground job; `bg` resumes it in the background, `fg` brings a background/stopped job back to the foreground**
5. What does a process's "niceness" value control, and what's the range? → **Its scheduling priority relative to other processes; ranges from -20 (highest priority) to 19 (lowest)**
6. Why would you check `ps -ef --forest` or `pstree` during incident response? → **To see parent-child relationships, helping identify what spawned a suspicious process**

## 12. Key Points

- **ps aux** / **ps -ef** show a snapshot of processes; **top**/**htop** show live, continuously updating views.
- Processes have a **PID** and **PPID**; PID 1 is always the init system (systemd).
- `kill` sends **signals**, not a direct "stop" command — **SIGTERM** (graceful, default) should be tried before **SIGKILL** (force).
- **nohup**, **disown**, and **tmux/screen** keep processes running past a terminal session's end.
- **nice/renice** control CPU scheduling priority (-20 highest to 19 lowest).
- Process inspection (`ps`, `pstree`, `lsof -p`) is a core incident-response skill for identifying and terminating malicious activity.

---
*Related: [[Process_Lifecycle_and_IPC]], [[Linux_Architecture]], [[Systemd_and_Services]]*
