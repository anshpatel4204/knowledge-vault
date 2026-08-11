## 1. Introduction

Linux's architecture is organized into distinct layers, from hardware at the bottom to user applications at the top, with the **kernel** acting as the central mediator between them. Understanding this layered structure — and specifically the boundary between **kernel space** and **user space** — is fundamental to understanding how permissions, system calls, and security boundaries actually work.

## 2. The Layered Architecture

```
┌─────────────────────────────────────┐
│   User Applications (Shell, Apps)    │  ← User Space
├─────────────────────────────────────┤
│   System Libraries (glibc, etc.)     │  ← User Space
├─────────────────────────────────────┤
│   System Call Interface              │  ← Boundary
├─────────────────────────────────────┤
│   Kernel (Process/Memory/File/       │  ← Kernel Space
│   Device management, Networking)     │
├─────────────────────────────────────┤
│   Hardware (CPU, RAM, Disk, NIC)     │
└─────────────────────────────────────┘
```

## 3. Kernel Space vs User Space

| Aspect | Kernel Space | User Space |
|---|---|---|
| Privilege | Full, unrestricted hardware access (Ring 0) | Restricted, mediated access (Ring 3) |
| Contains | Kernel code, device drivers, core subsystems | Applications, shells, user processes |
| Crash Impact | Can crash the entire system (kernel panic) | Typically isolated to that one process |
| Access to hardware | Direct | Only via system calls |

This separation is a fundamental **security boundary** — a compromised user-space application is normally contained, while a kernel-space vulnerability (exploited via a kernel bug or malicious kernel module) can compromise the entire system, which is exactly why kernel exploits are the most severe class of Linux privilege escalation (see [[Linux_Privilege_Escalation]]).

## 4. System Calls (Syscalls)

Since user-space applications can't directly touch hardware or kernel-managed resources, they request services from the kernel via **system calls** — a strictly defined interface (e.g., `open()`, `read()`, `write()`, `fork()`, `execve()`, `socket()`) that transitions execution from user mode into kernel mode to perform the privileged operation, then returns control back.

```
Application calls read() 
   → CPU switches to kernel mode (via a trap/interrupt) 
   → Kernel performs the actual disk read 
   → Result returned, CPU switches back to user mode
```

**Security relevance:** monitoring/filtering syscalls (via tools like `strace`, `seccomp`, or auditd) is a core technique in intrusion detection, malware analysis, and sandboxing/containerization.

## 5. Kernel Responsibilities

| Subsystem | Responsibility |
|---|---|
| Process Scheduler | Decides which process runs on the CPU and for how long (see [[Process_Management]]) |
| Memory Manager | Allocates/protects RAM, handles virtual memory and swapping |
| Virtual File System (VFS) | Provides a unified interface to different filesystem types (ext4, xfs, NTFS, etc.) |
| Device Drivers | Interface between the kernel and physical hardware |
| Network Stack | Implements the TCP/IP protocol suite in-kernel |
| Inter-Process Communication (IPC) | Pipes, sockets, shared memory, signals for process communication (see [[Process_Lifecycle_and_IPC]]) |

## 6. Monolithic Kernel Design

Linux uses a **monolithic kernel** architecture — most core services (process management, memory management, device drivers, filesystems, networking) run together in kernel space as a single large program, communicating via direct function calls (fast, but a bug in any part can affect the whole kernel).

| Kernel Type | Description | Example |
|---|---|---|
| Monolithic | All core services run in kernel space as one large program | Linux, traditional Unix |
| Microkernel | Only a minimal core (IPC, scheduling, basic memory management) runs in kernel space; other services (drivers, filesystems) run as separate user-space processes | MINIX, QNX, seL4 |
| Hybrid | A middle ground — some services run in kernel space for performance, others in user space | Windows NT kernel, macOS (XNU) |

**Loadable Kernel Modules (LKMs)** let Linux dynamically load/unload driver and subsystem code at runtime without rebooting or recompiling the whole kernel — giving Linux some of a microkernel's flexibility while retaining monolithic performance for the code that stays loaded. This is also a notable attack surface: malicious kernel modules ("rootkits") can be loaded to gain deep, hard-to-detect persistence.

```bash
lsmod                    # list loaded kernel modules
modprobe <module_name>   # load a module
rmmod <module_name>      # remove a module
```

## 7. The Shell's Role in the Architecture

The **shell** (see [[Linux_Shell_Basics]]) is a user-space program that interprets commands and translates them into system calls/program executions on the user's behalf — it is not part of the kernel itself, and different shells (bash, zsh, sh) are simply different user-space programs offering this interpretation layer.

## 8. Privilege Rings (CPU-Level Concept)

Modern CPUs implement hardware-enforced privilege levels ("rings"), and Linux maps onto them:

| Ring | Privilege | Linux Usage |
|---|---|---|
| Ring 0 | Highest — full hardware access | Kernel |
| Ring 1-2 | Intermediate (rarely used in practice on Linux) | Unused by standard Linux |
| Ring 3 | Lowest — restricted | User-space applications |

This hardware-enforced separation is what makes it *possible* for the kernel to safely mediate access between untrusted user processes and shared hardware/resources.

## 9. Root vs Non-Root — The User-Space Privilege Model

Within user space itself, Linux enforces a further access control layer via users/permissions (see [[File_Permissions]], [[User_and_Group_Management]]) — **root (UID 0)** bypasses most permission checks, while ordinary users are restricted, and privilege escalation attacks specifically target the boundary between these two levels.

## 10. Interview Questions

1. What's the key architectural difference between kernel space and user space? → **Kernel space has full, unrestricted hardware access; user space is restricted and must go through system calls**
2. What is a system call, and why is it necessary? → **A defined interface letting user-space programs request privileged kernel operations (e.g., file/network I/O) safely**
3. Is Linux a monolithic or microkernel design? → **Monolithic (with loadable kernel modules for flexibility)**
4. Why are kernel-level vulnerabilities considered more severe than user-space application vulnerabilities? → **A kernel compromise can affect the entire system, not just one isolated process**
5. What are Loadable Kernel Modules, and why are they also a security concern? → **They let drivers/subsystems load dynamically without a reboot; malicious modules can be used as stealthy rootkits for persistence**
6. What CPU privilege ring does the Linux kernel run in? → **Ring 0**

## 11. Key Points

- Architecture layers: **Hardware → Kernel (kernel space) → System Call Interface → Libraries/Applications (user space)**.
- **System calls** are the only sanctioned bridge between user space and kernel space.
- Linux uses a **monolithic kernel** with **Loadable Kernel Modules** for dynamic extensibility.
- Kernel-space compromise (via a kernel exploit or malicious module/rootkit) is far more severe than user-space compromise — this boundary is central to Linux security reasoning.
- The **shell** is just a user-space program, not part of the kernel.

---
*Related: [[Linux_Basics]], [[Process_Management]], [[Linux_Privilege_Escalation]], [[Linux_Boot_Process]]*
