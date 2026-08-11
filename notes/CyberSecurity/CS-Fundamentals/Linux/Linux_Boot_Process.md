## 1. Introduction

The **Linux boot process** is the sequence of steps a system goes through from powering on to reaching a usable login prompt. Understanding this sequence is essential for troubleshooting boot failures, hardening a system, and — from a security perspective — understanding where persistence mechanisms (bootkits, malicious init scripts/services) can be planted.

## 2. Boot Process Overview

```
Power On
   → BIOS/UEFI (POST, hardware init)
      → Bootloader (GRUB)
         → Kernel Load & Init
            → Initial RAM Disk (initrd/initramfs)
               → init/systemd (PID 1)
                  → Targets/Runlevels (services start)
                     → Login Prompt (getty) / Display Manager
```

## 3. Stage 1 — BIOS/UEFI

The firmware built into the motherboard performs **POST (Power-On Self-Test)**, checking hardware, then locates and hands control to a bootable device's bootloader.

| Aspect | BIOS (Legacy) | UEFI (Modern) |
|---|---|---|
| Partition scheme | MBR (Master Boot Record) | GPT (GUID Partition Table) |
| Boot process | Reads first 512 bytes of disk | Reads an EFI System Partition (ESP) with executable boot files |
| Security features | None built-in | **Secure Boot** — cryptographically verifies bootloader/kernel signatures before executing, preventing unsigned/malicious bootloaders (a defense against bootkits) |

## 4. Stage 2 — Bootloader (GRUB)

**GRUB (GRand Unified Bootloader)** is the standard Linux bootloader — it presents a boot menu (if configured), loads the selected **kernel** into memory, and loads the **initial RAM disk**.

```bash
# GRUB configuration (Debian/Ubuntu)
/etc/default/grub          # main config
/boot/grub/grub.cfg        # generated config (don't edit directly)
update-grub                # regenerate grub.cfg after changes
```

**Security relevance:** an unprotected GRUB menu can allow booting into single-user/rescue mode without a password, granting root access without authentication — hardening involves setting a GRUB password and restricting edit access.

## 5. Stage 3 — Kernel Initialization

GRUB loads the **Linux kernel** (`/boot/vmlinuz-<version>`) into memory. The kernel initializes core subsystems: memory management, process scheduler, device drivers, and mounts the **initial RAM disk**.

## 6. Stage 4 — initrd/initramfs

The **initial RAM filesystem** is a small, temporary root filesystem loaded into memory, containing just enough drivers/tools to mount the *real* root filesystem (which might require specific drivers, be on RAID/LVM, or be encrypted). Once the real root filesystem is mounted, the kernel hands off (`pivot_root` / `switch_root`) and initrd is discarded.

## 7. Stage 5 — init / systemd (PID 1)

The kernel starts the very first user-space process, always assigned **PID 1**, which becomes the ancestor of every other process on the system.

| init System | Description | Distros |
|---|---|---|
| systemd | Modern standard — parallelized startup, dependency-based service management, extensive tooling (`systemctl`, `journalctl`) | Most modern distros (Ubuntu, Fedora, RHEL, Debian) |
| SysVinit | Legacy — sequential startup via numbered scripts in `/etc/init.d/`, runlevels | Older systems, some minimal distros |
| Upstart | Event-based init used briefly by Ubuntu before systemd adoption | Legacy Ubuntu versions |
| OpenRC | Lightweight alternative | Gentoo, Alpine |

See [[Systemd_and_Services]] for full systemd detail.

## 8. Runlevels (SysVinit) vs Targets (systemd)

| SysVinit Runlevel | systemd Target | Purpose |
|---|---|---|
| 0 | poweroff.target | Halt the system |
| 1 | rescue.target | Single-user/maintenance mode |
| 3 | multi-user.target | Full multi-user, no GUI (typical server mode) |
| 5 | graphical.target | Multi-user with GUI |
| 6 | reboot.target | Reboot |

```bash
systemctl get-default          # show default target
systemctl set-default multi-user.target
systemctl isolate rescue.target  # switch to rescue mode now
```

## 9. Stage 6 — Login

Once the target system state is reached, either a **getty** process presents a text login prompt on a terminal (TTY), or a **display manager** (GDM, SDDM, LightDM) presents a graphical login screen.

## 10. Boot Process Security Considerations

| Concern | Description | Mitigation |
|---|---|---|
| Unprotected GRUB menu | Allows booting to single-user/root shell without authentication | Set a GRUB password, restrict boot parameter editing |
| Bootkits | Malware infecting the bootloader/firmware itself, executing before the OS and its security tools even load | UEFI **Secure Boot**, firmware integrity verification |
| Malicious init scripts/services | Persistence via a rogue systemd service or init script that starts automatically at boot | Regularly audit `systemctl list-unit-files`, `/etc/init.d/`, and startup-related cron/rc files |
| Physical access boot manipulation | Booting from external media (USB) to bypass OS-level protections entirely and mount/modify the disk | Disk encryption (LUKS), BIOS/UEFI boot order password, disabling external boot devices |

## 11. Useful Boot-Related Commands

```bash
systemd-analyze              # show boot time breakdown
systemd-analyze blame        # show which services took longest to start
journalctl -b                # view logs from the current boot
journalctl -b -1             # view logs from the previous boot
dmesg                        # view kernel ring buffer messages (includes boot messages)
```

## 12. Interview Questions

1. What are the six main stages of the Linux boot process in order? → **BIOS/UEFI → Bootloader (GRUB) → Kernel Init → initrd/initramfs → init/systemd (PID 1) → Login**
2. What's the difference between BIOS and UEFI in terms of security? → **UEFI supports Secure Boot, cryptographically verifying bootloader/kernel signatures; legacy BIOS has no such protection**
3. What is the initrd/initramfs used for? → **A temporary RAM filesystem with just enough drivers to mount the real root filesystem before handoff**
4. What process always has PID 1, and what init systems can fill that role? → **The init process — systemd (modern standard), SysVinit, Upstart, OpenRC**
5. Why is an unprotected GRUB menu a security risk? → **It can allow booting into single-user/rescue mode with root access, bypassing normal login authentication**
6. What is a bootkit? → **Malware that infects the bootloader/firmware, executing before the OS and its security tools load, providing deep persistence**

## 13. Key Points

- Boot sequence: **BIOS/UEFI → GRUB → Kernel → initrd → init/systemd (PID 1) → Login**.
- **UEFI Secure Boot** cryptographically verifies boot components, defending against bootkits — legacy BIOS has no equivalent.
- **systemd** (targets) has replaced **SysVinit** (runlevels) as the standard init system on most modern distros.
- Boot-stage security concerns: unprotected GRUB (single-user root access), bootkits, malicious startup services, and physical boot-media attacks — mitigated by GRUB passwords, Secure Boot, and disk encryption.

---
*Related: [[Linux_Architecture]], [[Systemd_and_Services]], [[Linux_Privilege_Escalation]]*
