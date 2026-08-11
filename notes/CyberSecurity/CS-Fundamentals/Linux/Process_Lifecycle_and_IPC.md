## 1. Introduction

This note goes one level deeper than [[Process_Management]] — into **how** processes are actually created, how they end, and how separate processes communicate with each other (IPC). This is foundational for understanding malware behavior (process injection, fork bombs), daemonization, and debugging complex multi-process applications.

## 2. Process Creation — fork() and exec()

Linux creates new processes through a two-step pattern almost universally used by shells and system tools:

### fork()
Creates a near-identical **copy** of the calling process — the new process (**child**) gets its own PID but otherwise starts as a duplicate of the parent's memory, open files, and execution state at that exact point.

```
Before fork(): 1 process (PID 100)
After fork():  2 processes (PID 100 = parent, PID 101 = child)
                Both continue executing from the same point onward
```

### exec() family
Replaces the **current** process's memory image with a new program entirely — the PID stays the same, but the running code changes completely.

**The classic combination:** a shell forks a child process, and that child immediately calls `exec()` to replace itself with the requested program (e.g., `ls`) — this is literally what happens every time you run a command.

```
Shell (PID 500)
   → fork() → Child (PID 501, still running shell code momentarily)
      → exec("ls") → Child (PID 501) is now running `ls` instead
```

## 3. Process Termination and exit()

A process ends by calling `exit()` (or being terminated by a signal — see [[Process_Management]]), which returns an **exit status** (0-255) to its parent, and releases most of its resources — except its entry in the process table, until the parent collects that status.

## 4. Zombie Processes

When a child process **exits** but its **parent hasn't yet called `wait()`** to collect its exit status, the child becomes a **zombie** — it's finished executing and consumes no real resources except a process table slot, but it lingers, showing as `Z` state in `ps`.

**Cause:** poorly written parent code that forks children but never reaps their exit status.
**Fix:** the parent process must call `wait()`/`waitpid()`; if the parent itself has already died, the kernel automatically reparents zombies to `init`/systemd (PID 1), which is designed to reap them.

## 5. Orphan Processes

A process whose **parent has terminated** before it did — the kernel automatically **reparents** orphans to PID 1 (init/systemd), which takes over responsibility for eventually reaping them when they finish.

| Concept | What's "stuck" | Cause |
|---|---|---|
| Zombie | The child (finished but not reaped) | Parent hasn't called wait() |
| Orphan | The parent relationship (child now un-parented) | Parent died before the child finished |

## 6. Daemons

A **daemon** is a background process, detached from any controlling terminal, typically started at boot and running for the system's lifetime (web servers, SSH daemon, cron). Modern daemons are usually managed by **systemd** (see [[Systemd_and_Services]]) rather than manually "daemonizing" themselves the old-fashioned way (double-forking, detaching from the terminal, redirecting standard streams).

**Naming convention:** daemon process names conventionally end in "d" — `sshd`, `httpd`, `crond`, `systemd` itself.

## 7. Inter-Process Communication (IPC)

Mechanisms allowing separate processes to exchange data or synchronize:

| Mechanism | Description |
|---|---|
| Pipes (`\|`) | Unidirectional data flow between related processes (e.g., parent-child), see [[Piping_Redirection_and_Operators]] |
| Named Pipes (FIFOs) | Like pipes, but exist as a filesystem path, allowing unrelated processes to communicate (see [[File_Types_and_Inodes]]) |
| Signals | Simple asynchronous notifications (SIGTERM, SIGKILL, etc. — see [[Process_Management]]) |
| Sockets | Bidirectional communication, either local (Unix domain sockets) or over a network (TCP/UDP sockets) |
| Shared Memory | Multiple processes accessing the same region of RAM directly — fastest IPC method, but requires careful synchronization |
| Message Queues | Structured messages passed between processes via the kernel, with priority/ordering |
| Semaphores | Synchronization primitives preventing race conditions when multiple processes access shared resources |

```bash
ls -la /var/run/docker.sock          # example of a Unix domain socket used for local IPC
ipcs                                    # show active System V IPC resources (shared memory, semaphores, message queues)
```

## 8. Threads vs Processes

| Aspect | Process | Thread |
|---|---|---|
| Memory | Own separate address space | Shares address space with other threads in the same process |
| Creation cost | Higher (fork is relatively expensive) | Lower (lighter-weight) |
| Communication | Requires explicit IPC | Can directly share memory/variables |
| Crash isolation | A crashed process doesn't directly crash others | A crashed thread can potentially corrupt the whole process |
| Visibility | Distinct PID | Threads share a PID, but have distinct Thread IDs (TIDs) |

```bash
ps -eLf                # show threads (LWPs) alongside processes
top -H                   # show threads in top
```

## 9. Security Relevance

| Concept | Security Angle |
|---|---|
| fork()/exec() | Malware "process injection" and "process hollowing" techniques abuse these primitives to run malicious code under the guise of a legitimate process |
| Fork bombs | A malicious/accidental pattern (`:(){ :\|:& };:`) that recursively forks processes until system resources are exhausted (a DoS against the local system) |
| Zombie/Orphan processes | Excessive zombies can exhaust the process table (a DoS vector); orphans reparenting to PID 1 is a normal, expected kernel behavior, not inherently malicious |
| Unix domain sockets | Misconfigured permissions on sockets like the Docker socket can allow privilege escalation (see [[Linux_Privilege_Escalation]]) |
| Shared memory/IPC | Improperly secured IPC channels can be abused for local privilege escalation or data leakage between processes of different trust levels |

## 10. Interview Questions

1. What's the difference between `fork()` and `exec()`? → **`fork()` creates a duplicate child process; `exec()` replaces a process's current memory image with a new program, keeping the same PID**
2. What is a zombie process, and how does it get cleaned up? → **A finished child process whose exit status hasn't been collected by its parent; cleaned up when the parent calls wait(), or reparented to init for reaping if the parent has died**
3. What's the difference between a zombie and an orphan process? → **A zombie is a finished child waiting to be reaped; an orphan is a still-running child whose parent has already died and gets reparented to PID 1**
4. Name three IPC mechanisms in Linux. → **Pipes, sockets, shared memory (also: signals, message queues, semaphores, named pipes)**
5. What's the key architectural difference between a process and a thread? → **Processes have separate memory address spaces; threads within the same process share memory**
6. What is a fork bomb, and what kind of attack is it? → **A recursively self-forking process pattern that exhausts system resources — a local denial-of-service attack**

## 11. Key Points

- New processes are created via **fork()** (duplicate) followed by **exec()** (replace with a new program) — the pattern behind every shell command execution.
- A **zombie** is a finished-but-unreaped child; an **orphan** is a still-running child whose parent died and gets reparented to PID 1.
- **Daemons** are long-running background processes, conventionally named ending in "d," managed today mostly via systemd.
- IPC mechanisms — **pipes, sockets, shared memory, signals, message queues, semaphores** — let separate processes communicate/synchronize.
- These primitives are directly relevant to security: **fork bombs**, **process injection**, and **misconfigured IPC socket permissions** are all real attack techniques built on this foundation.

---
*Related: [[Process_Management]], [[Linux_Architecture]], [[Systemd_and_Services]], [[Linux_Privilege_Escalation]]*
