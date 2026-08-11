## 1. Introduction

**Linux** is a free, open-source, Unix-like operating system kernel, originally created by **Linus Torvalds** in 1991 as a personal project while he was a student. Today "Linux" commonly refers to the entire operating system built around that kernel — more precisely called **GNU/Linux**, since most of the surrounding userland tools (shell, compilers, utilities) come from the **GNU Project**, started by Richard Stallman in 1983 to build a free Unix-compatible OS.

## 2. Why Linux Matters in Cybersecurity

Linux runs the vast majority of internet servers, cloud infrastructure, and embedded/IoT devices — and virtually every major penetration testing, forensics, and security distribution (Kali Linux, Parrot OS, REMnux, SIFT) is Linux-based. Deep command-line fluency in Linux is foundational for SOC work, VAPT, malware analysis, and system administration.

## 3. Kernel vs Operating System

| Term | Definition |
|---|---|
| Kernel | The core program managing hardware, memory, processes, and system calls — "Linux" technically refers only to this |
| Operating System (Distro) | The kernel + userland tools (shell, libraries, package manager, GUI, utilities) packaged together — e.g., Ubuntu, Fedora, Debian |
| GNU/Linux | The technically precise term acknowledging that most core utilities come from the GNU Project, combined with the Linux kernel |

## 4. Linux vs Unix vs Windows

| Aspect | Linux | Unix | Windows |
|---|---|---|---|
| License | Free, open-source (GPL) | Historically proprietary (various flavors: Solaris, AIX, HP-UX) | Proprietary |
| Source Availability | Fully open | Varies by vendor | Closed |
| Architecture Influence | Unix-like, POSIX-compliant | Original inspiration for Linux | Different lineage (NT kernel) |
| Typical Use | Servers, cloud, embedded, security tools, increasingly desktop | Legacy enterprise/mainframe systems | Desktop, enterprise, gaming |

## 5. The Linux Philosophy (Unix Philosophy)

Linux inherits the classic **Unix philosophy**, still visible in how its tools are designed:

- **Do one thing and do it well** — small, focused utilities (`grep`, `cut`, `sort`) rather than monolithic programs.
- **Everything is a file** — devices, processes, and system information are represented and interacted with as files (see [[File_Types_and_Inodes]]).
- **Compose simple tools together** — via piping (see [[Piping_Redirection_and_Operators]]), complex tasks are built from chains of simple commands.
- **Write programs that handle text streams** — since text is the universal, composable interface.
- **Prefer plain text configuration** — most system configuration lives in human-readable text files (mostly under `/etc`).

## 6. Open Source and Licensing

Linux is licensed under the **GPL (GNU General Public License)**, a **copyleft** license requiring that derivative works also remain open source — this is a key reason Linux's ecosystem has grown so large and collaboratively, and differs meaningfully from permissive licenses (MIT, Apache) that don't carry this requirement forward.

## 7. Major Linux Distributions ("Distros")

A **distribution** packages the Linux kernel with a set of userland tools, a package manager, and often a desktop environment, into a ready-to-use OS.

| Family | Package Manager | Examples |
|---|---|---|
| Debian-based | APT (`.deb`) | Debian, Ubuntu, Kali Linux, Parrot OS, Linux Mint |
| Red Hat-based | YUM/DNF (`.rpm`) | RHEL, Fedora, CentOS/Rocky Linux/AlmaLinux |
| Arch-based | Pacman | Arch Linux, Manjaro |
| SUSE-based | Zypper (`.rpm`) | openSUSE, SLES |
| Independent/Other | Varies | Gentoo (source-based), Alpine (minimal, container-focused), Slackware |

**Security-focused distributions:** Kali Linux and Parrot OS (penetration testing, pre-loaded with security tools), REMnux (malware analysis), SIFT Workstation (digital forensics), Tails (privacy/anonymity, amnesic).

## 8. Linux Release Models

| Model | Description | Examples |
|---|---|---|
| Fixed/Point Release | Stable, versioned releases with defined support periods | Ubuntu LTS, Debian Stable, RHEL |
| Rolling Release | Continuously updated, no discrete version numbers | Arch Linux, openSUSE Tumbleweed |

## 9. GUI vs CLI

Linux can run with or without a graphical interface:

| Mode | Description |
|---|---|
| CLI-only (headless) | No graphical environment — common on servers, minimizes resource usage and attack surface |
| Desktop Environment (DE) | Full GUI — GNOME, KDE Plasma, XFCE, Cinnamon |
| Window Manager (WM) | Lighter-weight GUI component managing window placement without a full DE — i3, bspwm |

Servers overwhelmingly run headless; most day-to-day cybersecurity/sysadmin work happens through the **shell** (see [[Linux_Shell_Basics]]) regardless of whether a GUI is present.

## 10. Key Linux Characteristics

- **Multi-user** — many users can be logged in and running processes simultaneously (see [[User_and_Group_Management]]).
- **Multi-tasking** — the kernel schedules many processes concurrently (see [[Process_Management]]).
- **Case-sensitive filesystem** — `File.txt` and `file.txt` are different files.
- **Everything is a file** — including devices (`/dev`), processes (`/proc`), and even kernel parameters (`/sys`).
- **Highly configurable and scriptable** — nearly every aspect of the system can be automated via shell scripting (see [[Shell_Scripting]]).

## 11. Interview Questions

1. Who created the Linux kernel, and in what year? → **Linus Torvalds, 1991**
2. What's the technically precise term for what's commonly called "Linux"? → **GNU/Linux**
3. What license is Linux released under, and what does that imply? → **GPL — a copyleft license requiring derivative works to remain open source**
4. Name two Debian-based and two Red Hat-based distributions. → **Debian-based: Ubuntu, Kali Linux; Red Hat-based: Fedora, RHEL/CentOS**
5. What Unix philosophy principle explains why Linux has so many small, focused commands instead of few large ones? → **"Do one thing and do it well"**
6. Why do most Linux servers run without a GUI? → **Reduces resource usage and attack surface; administration is done via the shell/CLI**

## 12. Key Points

- Linux = the **kernel**; "Linux" as commonly used = kernel + GNU userland tools = **GNU/Linux**.
- Follows the **Unix philosophy**: small composable tools, "everything is a file," plain-text configuration.
- Licensed under the **GPL** (copyleft, open source).
- **Distributions** package the kernel with tools/package managers — Debian-based (APT) and Red Hat-based (YUM/DNF) are the two dominant families.
- Security work (VAPT, forensics, SOC) is almost entirely Linux-centric — Kali, Parrot, REMnux, SIFT are standard tools of the trade.

---
*Related: [[Linux_Architecture]], [[Linux_File_System_Hierarchy]], [[Linux_Shell_Basics]]*
