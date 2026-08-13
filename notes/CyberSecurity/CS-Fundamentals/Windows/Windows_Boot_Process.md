## 1. Introduction

Understanding how Windows boots — from power-on to a usable desktop — matters for troubleshooting, for understanding **boot-level persistence/rootkit techniques** (bootkits, malicious drivers loaded early), and for grasping why features like **Secure Boot** and **BitLocker** are positioned where they are in the chain.

## 2. The Boot Sequence — High Level

```
Power On
  → Firmware (BIOS or UEFI) — POST (Power-On Self Test)
    → Boot Manager (bootmgr / bootmgfw.efi)
      → Winload (winload.exe / winload.efi)
        → Kernel Initialization (ntoskrnl.exe + HAL)
          → Session Manager (smss.exe)
            → Windows Logon (winlogon.exe) + Local Security Authority (lsass.exe)
              → User Desktop (explorer.exe)
```

## 3. Firmware Stage — BIOS vs UEFI

| Aspect | Legacy BIOS | UEFI (modern standard) |
|---|---|---|
| Boot mechanism | Reads MBR (Master Boot Record) from disk | Reads the EFI System Partition (ESP), a FAT32 partition with `.efi` boot files |
| Partition style | MBR (max ~2TB, 4 primary partitions) | GPT (GUID Partition Table — larger disks, more partitions) |
| Security features | None built-in | **Secure Boot** — cryptographically verifies each boot component's signature |
| Boot file | Boot sector code | `bootmgfw.efi` |

**Secure Boot** is the key security-relevant addition — it verifies the Boot Manager, OS loader, and boot drivers are signed by a trusted authority before executing them, specifically to block **bootkits** (malware that hijacks the boot chain before the OS/AV even loads).

## 4. Windows Boot Manager

`bootmgr` (BIOS) or `bootmgfw.efi` (UEFI) is the first Windows-specific code that runs. It reads the **Boot Configuration Data (BCD)** store — a binary registry-like database (replacing the old `boot.ini`) — to determine which OS/partition to load, then hands off to **winload**.

```cmd
bcdedit /enum          :: view current boot configuration
bcdedit /enum all         :: view all boot entries including recovery
```

## 5. Winload and Kernel Initialization

`winload.exe` (or `winload.efi` on UEFI systems) loads:

1. `ntoskrnl.exe` — the kernel itself
2. `hal.dll` — the Hardware Abstraction Layer
3. Boot-critical drivers (marked `BOOT_START` in their registry service entries — e.g., disk controller drivers needed just to keep reading from disk)
4. The **SYSTEM** registry hive (see [[Windows_Registry]]), needed to know which drivers/services to start next

Once these are in memory, control passes to the kernel, which initializes the Executive subsystems (Object Manager, Memory Manager, etc. — see [[Windows_Architecture]]) and starts the **Session Manager**.

## 6. Session Manager and Subsystems

`smss.exe` (Session Manager Subsystem) is the first user-mode process created by the kernel. It:

- Creates environment variables and the page file
- Starts the Win32 subsystem (`csrss.exe`)
- Launches `wininit.exe` (system session) which in turn starts:
  - `services.exe` — the **Service Control Manager**, which starts all `AUTO_START` services (see [[Windows_Processes_and_Services]])
  - `lsass.exe` — the **Local Security Authority Subsystem**, responsible for authentication (see [[Windows_Authentication]])

## 7. Logon and Desktop

`winlogon.exe` manages the interactive logon session — presenting the login screen, handling Ctrl+Alt+Del (the "Secure Attention Sequence," designed so malicious software can't spoof the login prompt), and invoking `lsass.exe` to validate credentials. On successful logon, `explorer.exe` starts as the shell, and the user reaches the desktop.

## 8. Safe Mode and Recovery

| Boot mode | Purpose |
|---|---|
| Normal boot | All drivers/services load |
| Safe Mode | Loads only essential drivers/services — used to troubleshoot malware, driver conflicts, boot failures |
| Safe Mode with Networking | Safe Mode + network drivers |
| Last Known Good Configuration (legacy) | Reverts to the registry control set from the last successful boot |
| Windows Recovery Environment (WinRE) | A minimal pre-boot OS for repair, System Restore, and command-line recovery |

```cmd
bcdedit /set {default} safeboot minimal      :: force next boot into Safe Mode
bcdedit /deletevalue {default} safeboot        :: revert to normal boot
```

## 9. Security Relevance — Boot-Level Attacks and Defenses

| Threat | Description |
|---|---|
| Bootkit | Malware that infects the MBR/boot sector or EFI partition, executing before the OS and any AV/EDR |
| Malicious boot-start driver | A driver registered as `BOOT_START` can load before most security tooling initializes |
| Defense: Secure Boot | Verifies signatures of every boot-chain component against trusted keys stored in firmware |
| Defense: Trusted Boot | Extends verification into the kernel and boot-critical drivers/ELAM (Early Launch Anti-Malware) |
| Defense: Measured Boot + TPM | Hashes each boot stage into the TPM so remote attestation can detect tampering after the fact |

## 10. Interview Questions

1. What's the modern replacement for `boot.ini`, and what stores it? → **The Boot Configuration Data (BCD) store**, managed via `bcdedit`
2. What does Secure Boot actually verify? → **The cryptographic signature of the Boot Manager, OS loader, and boot-critical drivers before allowing them to execute**
3. What is the first user-mode process the Windows kernel creates? → **`smss.exe`, the Session Manager Subsystem**
4. Which process is responsible for authentication during logon? → **`lsass.exe`, the Local Security Authority Subsystem**
5. Why is a "BOOT_START" driver a notable security concern? → **It loads before most security software initializes, giving early, hard-to-detect execution — a technique used by bootkits/rootkits**
6. What's the difference between Secure Boot and Measured Boot? → **Secure Boot prevents unsigned/tampered components from executing; Measured Boot instead records hashes of what loaded (via TPM) so tampering can be detected/attested after the fact, even if execution wasn't blocked**

## 11. Key Points

- Boot order: **Firmware (BIOS/UEFI) → Boot Manager → Winload → Kernel init → Session Manager → Services/LSASS → Winlogon → Explorer**.
- **UEFI + GPT + Secure Boot** is the modern standard, replacing legacy BIOS/MBR and adding cryptographic verification of the boot chain.
- **BCD** (via `bcdedit`) replaced the old `boot.ini` for boot configuration.
- `smss.exe` → `wininit.exe` → `services.exe` (Service Control Manager) and `lsass.exe` (authentication) are the critical early user-mode processes.
- Boot-level persistence (bootkits, malicious `BOOT_START` drivers) is dangerous specifically because it executes before most defensive tooling — Secure Boot/Trusted Boot/Measured Boot exist to counter exactly this.

---
*Related: [[Windows_Basics]], [[Windows_Architecture]], [[Windows_Registry]], [[Windows_Processes_and_Services]], [[Windows_Security_Features]]*
