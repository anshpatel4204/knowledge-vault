## 1. Introduction

The **Windows Registry** is a centralized, hierarchical binary database storing configuration settings for the OS, installed applications, hardware, and per-user preferences — Windows' equivalent to the scattered plain-text config files under Linux's `/etc` (see [[Linux_File_System_Hierarchy]]). It is one of the two structures (alongside [[NTFS_File_System]]) that nearly everything else in Windows administration and security touches.

## 2. Registry Structure — Hives, Keys, Values

```
Hive (root)
  └── Key
        └── Subkey
              └── Value Name : Value Type : Value Data
```

| Term | Meaning |
|---|---|
| Hive | A top-level root, each backed by a file on disk |
| Key | Like a folder — a container for subkeys and values |
| Value | A named piece of data inside a key (has a name, type, and data) |

## 3. The Five Root Hives

| Hive | Abbreviation | Contains |
|---|---|---|
| HKEY_LOCAL_MACHINE | HKLM | System-wide hardware/software configuration (affects all users) |
| HKEY_CURRENT_USER | HKCU | Settings for the currently logged-in user (a view into HKEY_USERS) |
| HKEY_USERS | HKU | Settings for **all** loaded user profiles, keyed by SID |
| HKEY_CLASSES_ROOT | HKCR | File associations and COM object registration (merged view of HKLM\Software\Classes and HKCU equivalent) |
| HKEY_CURRENT_CONFIG | HKCC | Current hardware profile (pointer into HKLM) |

## 4. Key HKLM Subtrees

| Path | Purpose |
|---|---|
| `HKLM\SYSTEM` | Boot config, device drivers, services (`ControlSet00x`) |
| `HKLM\SOFTWARE` | Installed software configuration |
| `HKLM\SAM` | Local user account database (Security Account Manager — see [[Windows_Authentication]]) |
| `HKLM\SECURITY` | LSA policy, cached credentials (locked even from Administrators by default) |
| `HKLM\HARDWARE` | Volatile, hardware detected at boot (rebuilt every boot) |

## 5. Value Types

| Type | Meaning |
|---|---|
| REG_SZ | A string |
| REG_DWORD | 32-bit number |
| REG_QWORD | 64-bit number |
| REG_BINARY | Raw binary data |
| REG_MULTI_SZ | Multiple strings |
| REG_EXPAND_SZ | A string containing an environment variable to expand (e.g., `%SystemRoot%`) |

## 6. Registry-Backed Files on Disk

Hives aren't purely in-memory — they're backed by files, which is exactly why registry hives are a first-class **forensic artifact**:

| Hive | Backing file location |
|---|---|
| HKLM\SYSTEM | `C:\Windows\System32\config\SYSTEM` |
| HKLM\SOFTWARE | `C:\Windows\System32\config\SOFTWARE` |
| HKLM\SAM | `C:\Windows\System32\config\SAM` |
| HKLM\SECURITY | `C:\Windows\System32\config\SECURITY` |
| HKCU (per user) | `C:\Users\<user>\NTUSER.DAT` |

Forensic investigators pull these files directly (often from a disk image, since live hives are locked) to reconstruct system history — installed programs, USB device history, run history (`RunMRU`), and more.

## 7. Tools — GUI and CLI

```cmd
regedit                                     :: GUI registry editor

reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run   :: list run keys
reg add HKCU\Software\MyApp /v Setting /t REG_SZ /d "value"      :: add a value
reg delete HKCU\Software\MyApp /v Setting /f                       :: delete a value
reg export HKLM\SOFTWARE backup.reg                                  :: export a hive/key
reg import backup.reg                                                  :: import back
```

```powershell
Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"
New-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting" -Value "value" -PropertyType String
Remove-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting"
```

## 8. Registry Permissions

Like files, registry keys have their own **ACLs**, viewable/editable via Regedit's Permissions dialog or `icacls`-style tooling (specifically `regini`/PowerShell `Get-Acl`/`Set-Acl` against the `Registry::` provider). Misconfigured, overly-permissive ACLs on service-related registry keys are a known privilege-escalation vector (see [[Windows_Privilege_Escalation]]).

## 9. Security Relevance — Persistence via the Registry

The registry is one of the most common places malware establishes **persistence** (surviving reboot/logoff):

| Location | Effect |
|---|---|
| `HKLM\...\CurrentVersion\Run` / `RunOnce` | Runs a program at every (or next) user logon, machine-wide |
| `HKCU\...\CurrentVersion\Run` | Same, but only for the current user |
| `HKLM\SYSTEM\CurrentControlSet\Services\<name>` | Registers a Windows service — can be set to auto-start (see [[Windows_Processes_and_Services]]) |
| `HKLM\...\Winlogon\Shell` / `Userinit` | Hijacks what runs when a user logs in, alongside/instead of `explorer.exe` |
| Image File Execution Options (IFEO) debugger key | Redirects execution of a legitimate binary to a malicious one |

These are covered in depth in [[Windows_Persistence_and_Lateral_Movement]]; recognizing them is a core blue-team skill during incident response.

## 10. Interview Questions

1. What are the five root registry hives? → **HKEY_LOCAL_MACHINE, HKEY_CURRENT_USER, HKEY_USERS, HKEY_CLASSES_ROOT, HKEY_CURRENT_CONFIG**
2. Where is a user's HKCU hive actually stored on disk? → **`NTUSER.DAT` in the user's profile folder**
3. Which hive holds local user account password hashes, and what protects it? → **`HKLM\SAM`; it's locked even from Administrators by default and requires SYSTEM-level access (or offline extraction) to read directly**
4. Name two registry locations commonly abused for persistence. → **`Run`/`RunOnce` keys (HKLM or HKCU) and the Services registry tree (`HKLM\SYSTEM\CurrentControlSet\Services`)**
5. Why are registry hives significant in digital forensics even after a machine is imaged offline? → **They're just files on disk (SYSTEM, SOFTWARE, SAM, NTUSER.DAT) that can be parsed independently of a running OS, revealing installed software, run history, USB history, and user activity**

## 11. Key Points

- The registry is a **hierarchical binary database**: hives → keys → values, mirroring `/etc` conceptually but structurally very different.
- **HKLM** governs machine-wide settings; **HKCU** governs the current user (a view into **HKU**).
- Hives are backed by real files (`SYSTEM`, `SOFTWARE`, `SAM`, `NTUSER.DAT`), making them a first-class forensic artifact.
- `reg` (CLI) and `regedit` (GUI) are the primary native tools; PowerShell treats the registry as a navigable provider (`HKLM:`, `HKCU:`).
- The registry is one of the **most common persistence mechanisms** for malware — `Run`/`RunOnce` keys and Services entries above all.

---
*Related: [[NTFS_File_System]], [[Windows_Processes_and_Services]], [[Windows_Authentication]], [[Windows_Persistence_and_Lateral_Movement]]*
