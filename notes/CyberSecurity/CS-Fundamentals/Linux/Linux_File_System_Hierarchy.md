## 1. Introduction

Linux organizes all files and directories into a single, unified tree starting at the **root directory (`/`)** — unlike Windows' separate drive letters (C:\, D:\), every storage device, partition, and even remote filesystem is **mounted** into this one tree at a specific location. The standard layout is defined by the **FHS (Filesystem Hierarchy Standard)**, ensuring consistency across distributions.

## 2. The Root Directory and Unified Tree

```
/
├── bin, sbin, usr, etc, var, home, root, tmp, dev, proc, sys, boot, lib, mnt, media, opt, srv
```

Everything — including other disks/partitions — attaches somewhere under `/` via **mounting** (see [[Disk_and_Storage_Management]]), rather than getting its own top-level drive letter.

## 3. Key FHS Directories

| Directory | Purpose |
|---|---|
| `/` | Root of the entire filesystem tree |
| `/bin` | Essential user command binaries (ls, cp, cat) needed even in single-user mode — often symlinked to `/usr/bin` on modern systems |
| `/sbin` | Essential system administration binaries (fdisk, iptables, reboot) |
| `/etc` | System-wide configuration files (plain text) — arguably the most important directory for administrators |
| `/home` | Personal directories for regular users (`/home/ansh`) |
| `/root` | The root user's home directory (separate from `/home` for reliability — accessible even if `/home` fails to mount) |
| `/var` | Variable data that changes frequently — logs (`/var/log`), spool files, caches |
| `/tmp` | Temporary files, typically cleared on reboot, world-writable |
| `/usr` | Secondary hierarchy for user-installed/read-only software, libraries, documentation (`/usr/bin`, `/usr/lib`, `/usr/share`) |
| `/lib`, `/lib64` | Essential shared libraries needed by binaries in `/bin` and `/sbin` |
| `/boot` | Bootloader files, kernel images (`vmlinuz`), initramfs — see [[Linux_Boot_Process]] |
| `/dev` | Device files representing hardware (disks, terminals, USB devices) — see [[File_Types_and_Inodes]] |
| `/proc` | Virtual filesystem exposing kernel/process information in real time (not real files on disk) |
| `/sys` | Virtual filesystem exposing kernel/device/driver information and tunable parameters |
| `/mnt` | Conventional temporary mount point for manually mounted filesystems |
| `/media` | Conventional mount point for removable media (USB drives, CDs) |
| `/opt` | Optional/third-party software packages, often self-contained |
| `/srv` | Data for services hosted on the system (web server content, FTP files) |
| `/lost+found` | Recovered file fragments after a filesystem check (fsck) on ext-family filesystems |

## 4. /etc — The Configuration Hub

`/etc` (historically "et cetera") holds nearly all system-wide configuration as plain text — a defining Unix philosophy trait (see [[Linux_Basics]]).

| File/Directory | Purpose |
|---|---|
| `/etc/passwd` | User account information (see [[User_and_Group_Management]]) |
| `/etc/shadow` | Hashed user passwords |
| `/etc/group` | Group definitions |
| `/etc/fstab` | Filesystem mount configuration |
| `/etc/hosts` | Static hostname-to-IP mappings |
| `/etc/hostname` | The system's hostname |
| `/etc/resolv.conf` | DNS resolver configuration |
| `/etc/ssh/sshd_config` | SSH daemon configuration |
| `/etc/crontab`, `/etc/cron.d/` | System-wide scheduled tasks (see [[Cron_and_Task_Scheduling]]) |
| `/etc/systemd/` | systemd unit files and configuration |

## 5. /proc — The Virtual Process Filesystem

`/proc` doesn't contain real files on disk — it's a **virtual filesystem** generated on-the-fly by the kernel, exposing live system and process information as if it were a normal filesystem, extremely useful for both administration and low-level troubleshooting/forensics.

| Path | Contains |
|---|---|
| `/proc/[PID]/` | Info about a specific running process (cmdline, environ, status, open file descriptors in `fd/`) |
| `/proc/cpuinfo` | CPU details |
| `/proc/meminfo` | Memory usage details |
| `/proc/version` | Kernel version |
| `/proc/net/` | Networking state (connections, routing table) |

```bash
cat /proc/cpuinfo
cat /proc/1234/cmdline    # command line of process 1234
ls /proc/1234/fd/          # open file descriptors of process 1234 — useful in incident response
```

## 6. /sys — The Virtual Sysfs Filesystem

Similar in spirit to `/proc`, but specifically exposes kernel device/driver/hardware information and tunable kernel parameters in a structured way, used heavily by tools like `udev` for device management.

## 7. Absolute vs Relative Paths

| Type | Description | Example |
|---|---|---|
| Absolute Path | Starts from root `/`, unambiguous regardless of current location | `/home/ansh/notes.txt` |
| Relative Path | Interpreted relative to the current working directory | `notes.txt` or `../notes.txt` |

**Special path shortcuts:**

| Symbol | Meaning |
|---|---|
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Current user's home directory |
| `-` | Previous working directory (used with `cd -`) |

## 8. Mount Points

A **mount point** is a directory where a separate filesystem/partition/device is attached into the unified tree. `/`, `/home`, `/boot`, and `/var` are commonly placed on **separate partitions** in production server setups — a deliberate security/reliability practice: e.g., putting `/tmp` and `/var` on their own partitions prevents a filled log or temp directory from consuming all space on the root filesystem and crashing the system.

```bash
df -h              # show mounted filesystems and usage
mount               # show current mounts
lsblk                # show block devices and their mount points
```

## 9. Filesystem Hierarchy Security Relevance

| Directory | Security Note |
|---|---|
| `/etc/passwd`, `/etc/shadow` | Prime targets for credential extraction — `/etc/shadow` should never be world-readable |
| `/tmp` | World-writable, a classic vector for symlink attacks and race conditions if scripts handle it carelessly |
| `/var/log` | Central location for security-relevant audit trails — see [[Log_Management]] |
| `/proc/[PID]/` | Used heavily in live incident response/forensics to inspect running (possibly malicious) processes |
| `/dev` | Device files here can sometimes be abused in privilege escalation if permissions are misconfigured |

## 10. Interview Questions

1. What standard defines the Linux directory layout? → **FHS (Filesystem Hierarchy Standard)**
2. What's the difference between `/bin` and `/usr/bin`? → **Historically `/bin` held essential binaries needed before `/usr` was mounted; on modern systems `/bin` is usually just a symlink to `/usr/bin`**
3. Why does `/root` exist separately from `/home`? → **So the root user's home directory remains accessible even if `/home` (potentially a separate partition) fails to mount**
4. What is `/proc`, and is it a real filesystem on disk? → **A virtual filesystem exposing live kernel/process information — not real files on disk, generated dynamically**
5. Why is it good practice to put `/var` and `/tmp` on separate partitions from `/`? → **Prevents a filled log or temp directory from consuming all root filesystem space and crashing the system**
6. What's the difference between an absolute and relative path? → **Absolute starts from root `/` and is unambiguous; relative is interpreted from the current working directory**

## 11. Key Points

- Linux uses a **single unified filesystem tree** rooted at `/` — no drive letters; devices are **mounted** into this tree.
- Key directories: `/etc` (config), `/var` (variable data/logs), `/home` (user data), `/bin`/`/sbin` (essential binaries), `/boot` (bootloader/kernel).
- `/proc` and `/sys` are **virtual filesystems** exposing live kernel/process data, not real disk files.
- Separating `/`, `/var`, `/tmp`, `/home` onto distinct partitions is a common reliability/security practice.
- `/etc/passwd` and `/etc/shadow` are high-value targets — file hierarchy knowledge underpins both administration and forensics work.

---
*Related: [[Linux_Basics]], [[File_Types_and_Inodes]], [[File_Permissions]], [[Disk_and_Storage_Management]]*
