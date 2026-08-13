## 1. Introduction

Like Linux, Windows enforces a hard boundary between **kernel mode** (Ring 0, unrestricted hardware access) and **user mode** (Ring 3, restricted, mediated access). But Windows' internal layering — the **HAL**, the **Executive**, subsystem DLLs, and the split between the **Win32 subsystem** and native NT services — is structured differently, and that structure directly explains why certain Windows attacks (kernel exploits, DLL injection, token manipulation) work the way they do.

## 2. The Layered Architecture

```
┌───────────────────────────────────────────┐
│  User Applications                          │  ← User Mode
├───────────────────────────────────────────┤
│  Subsystem DLLs (kernel32, user32, ntdll)    │  ← User Mode
├───────────────────────────────────────────┤
│  System Call Interface (ntdll → syscall)      │  ← Boundary
├───────────────────────────────────────────┤
│  Windows Executive (Object Mgr, I/O Mgr,       │
│  Memory Mgr, Security Reference Monitor, etc.)  │  ← Kernel Mode
├───────────────────────────────────────────┤
│  Kernel (ntoskrnl.exe) — scheduling, interrupts  │  ← Kernel Mode
├───────────────────────────────────────────┤
│  Hardware Abstraction Layer (HAL)                 │  ← Kernel Mode
├───────────────────────────────────────────┤
│  Hardware (CPU, RAM, Disk, NIC)                    │
└───────────────────────────────────────────┘
```

## 3. Kernel Mode vs User Mode

| Aspect | Kernel Mode | User Mode |
|---|---|---|
| Privilege | Full hardware access (Ring 0) | Restricted (Ring 3) |
| Contains | ntoskrnl.exe, HAL, drivers, Executive | Applications, subsystem DLLs, services |
| Crash impact | Blue Screen of Death (system-wide) | Isolated to that process |
| Access to hardware | Direct | Only via system calls through ntdll.dll |

A crash in kernel mode halts the entire OS (the infamous **BSOD**), while a user-mode crash is normally contained — the same security logic as Linux's kernel/user split (see [[Linux_Architecture]]).

## 4. The Hardware Abstraction Layer (HAL)

The **HAL** sits between the kernel and physical hardware, presenting a consistent interface so the same kernel code runs across different chipsets/motherboards without hardware-specific rewrites. This is one of Windows' key architectural differences from Linux, which handles hardware abstraction more through modular drivers than a single unifying layer.

## 5. The Windows Executive

The **Executive** (part of `ntoskrnl.exe`) is the collection of core kernel-mode components that provide the services applications rely on indirectly:

| Component | Responsibility |
|---|---|
| Object Manager | Manages all kernel objects (files, processes, events, mutexes) via a unified namespace |
| Process/Thread Manager | Creates/manages processes and threads (see [[Windows_Processes_and_Services]]) |
| Memory Manager | Virtual memory, paging, working sets |
| I/O Manager | Routes I/O requests to the correct device drivers |
| Security Reference Monitor (SRM) | Enforces access control — checks tokens against object ACLs on every access (see [[File_and_Folder_Permissions]], [[UAC_and_Privilege_Levels]]) |
| Cache Manager | File system caching |

The **Security Reference Monitor** is the single most security-relevant component here — every access check in Windows (file open, registry read, process handle grant) ultimately routes through it.

## 6. User Mode: Subsystems and DLLs

User mode applications don't call the kernel directly — they go through layered DLLs:

```
Application
   → kernel32.dll / user32.dll / advapi32.dll  (Win32 API)
      → ntdll.dll                                 (native API, wraps syscalls)
         → syscall instruction                        (transitions to kernel mode)
```

| DLL | Role |
|---|---|
| kernel32.dll | Core Win32 functions (files, processes, memory) |
| user32.dll | Windowing/UI functions |
| advapi32.dll | Registry, services, security APIs |
| ntdll.dll | Lowest user-mode layer — wraps raw system calls into the kernel |

**Security relevance:** malware frequently hooks or manually maps these DLLs (e.g., "unhooking ntdll" to evade EDR that hooks Win32/native API calls for monitoring) — understanding this call chain is foundational to both malware analysis and detection engineering.

## 7. Processes, Threads, and Handles

Windows represents nearly everything as an **object** managed by the Object Manager — files, registry keys, processes, threads, and synchronization primitives all have **handles**, opaque references a process uses to interact with them. This differs from Linux's "everything is a file" philosophy — Windows instead uses "everything is an object with a handle."

## 8. Windows vs Linux Kernel Design

| Design | Description | Example |
|---|---|---|
| Monolithic | Core services run together in kernel space | Linux |
| Hybrid | A large Executive runs in kernel mode for performance, but is modularized internally; some components (e.g., win32k.sys graphics subsystem) also live in kernel mode for speed | **Windows NT kernel** |
| Microkernel | Minimal kernel core; most services in user space | MINIX, QNX |

Windows NT is classified as a **hybrid kernel** — architecturally closer to a microkernel design on paper, but with performance-critical subsystems (like the graphics subsystem, `win32k.sys`) pulled into kernel mode for speed, blurring the line.

## 9. Interview Questions

1. What is the HAL and why does it exist? → **Hardware Abstraction Layer — isolates the kernel from hardware-specific differences so the same kernel binary runs across diverse hardware**
2. What Windows Executive component enforces access control on every object access? → **The Security Reference Monitor (SRM)**
3. Is the Windows NT kernel monolithic, microkernel, or hybrid? → **Hybrid — a large Executive with some performance-critical components (e.g., win32k.sys) running in kernel mode**
4. What's the call path from a Win32 API call down to the kernel? → **Application → kernel32.dll/user32.dll (Win32 API) → ntdll.dll (native API) → syscall instruction → kernel mode**
5. Why do EDR products and malware both care about ntdll.dll? → **It's the lowest user-mode layer wrapping every system call — EDR hooks it for visibility, malware "unhooks" or manually maps it to evade that visibility**

## 10. Key Points

- Windows enforces the same **kernel mode / user mode** hardware boundary as any modern OS, but layers it through the **HAL → Executive → subsystem DLLs → applications**.
- The **Security Reference Monitor** is the central access-control checkpoint for the entire OS.
- User-mode API calls funnel through **kernel32/user32/advapi32 → ntdll.dll → syscall**, a chain heavily relevant to both malware evasion and EDR detection.
- Windows NT is a **hybrid kernel** — more modular than a classic monolithic design, but not a pure microkernel either.
- Nearly everything in Windows is represented as an **object** accessed via a **handle**, managed centrally by the Object Manager.

---
*Related: [[Windows_Basics]], [[Windows_Boot_Process]], [[Windows_Processes_and_Services]], [[UAC_and_Privilege_Levels]]*
