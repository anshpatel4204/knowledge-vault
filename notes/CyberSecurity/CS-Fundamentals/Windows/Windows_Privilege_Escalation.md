## 1. Introduction

**Windows privilege escalation** is the process of turning limited access (a standard user, or a low-privilege service account) into higher privilege — Administrator or, ideally for an attacker, **SYSTEM**. It's the capstone that ties together nearly every earlier note in this folder: services, registry, tokens, permissions, and scheduled tasks are all, in practice, the raw material privilege escalation exploits.

## 2. Two Broad Categories

| Category | Description |
|---|---|
| **Vertical** | Gaining a *higher* privilege level than currently held (standard user → admin → SYSTEM) — the classic meaning of "privesc" |
| **Horizontal** | Gaining access to a *different* account at the same privilege level (compromising a peer user's session/credentials) |

This note focuses primarily on vertical escalation on a single Windows host; domain-wide escalation paths are covered in [[Active_Directory_Basics]] and [[Windows_Persistence_and_Lateral_Movement]].

## 3. Misconfigured Service Permissions

Covered in depth in [[Windows_Processes_and_Services]] — the classic pattern:

| Technique | Mechanism |
|---|---|
| Weak service binary permissions | A low-privileged user can overwrite the executable a SYSTEM-run service launches |
| Weak service registry permissions | Modifying the service's `ImagePath` registry value achieves the same result without touching any file |
| Unquoted service path | An unquoted path containing spaces (`C:\Program Files\App\svc.exe`) lets an attacker plant `C:\Program.exe`, which Windows tries first |

```cmd
sc qc VulnerableService                 :: check binary path and startup type
accesschk.exe -uwcqv "Authenticated Users" *      :: (Sysinternals) enumerate writable services
```

## 4. Weak File/Folder Permissions

Covered in [[File_and_Folder_Permissions]] — if a low-privileged user has write access to a directory or file that a privileged process later executes or loads from (an auto-run location, a DLL search path, a scheduled task's script), that's a direct path to running code at that process's privilege level.

```powershell
icacls "C:\Program Files\App" | findstr "F"        # look for Full Control granted to non-admins
```

## 5. Abusable Privileges (SeXXXPrivilege)

Covered conceptually in [[Users_Groups_and_SID]] — certain user rights, if assigned to an account an attacker controls, directly enable escalation:

| Privilege | Abuse path |
|---|---|
| `SeImpersonatePrivilege` / `SeAssignPrimaryTokenPrivilege` | Enables "Potato" family exploits (JuicyPotato, RoguePotato, PrintSpoofer) — abusing Windows' COM/NTLM local service behavior to coerce a SYSTEM authentication and impersonate the resulting token; commonly held by default by many service accounts |
| `SeBackupPrivilege` / `SeRestorePrivilege` | Read/write any file regardless of ACLs — enables direct extraction of SAM/SYSTEM registry hives for offline hash cracking |
| `SeDebugPrivilege` | Open a handle to (and inject into/read memory from) virtually any process, including SYSTEM ones |
| `SeLoadDriverPrivilege` | Load a malicious kernel driver — direct path to kernel-mode code execution |
| `SeTakeOwnershipPrivilege` | Seize ownership of protected objects, then grant yourself access |

```cmd
whoami /priv           :: check current privileges — this is often the very first privesc-hunting step
```

## 6. Registry-Based Escalation Vectors

| Vector | Mechanism |
|---|---|
| AlwaysInstallElevated | If both an HKLM and HKCU registry value enable this, any user can install an MSI package with SYSTEM privileges — a well-known, easy-to-check misconfiguration |
| Weak registry ACLs on service keys | Same effect as weak service *file* permissions, but via the registry's `ImagePath` value |
| Autorun/Run key hijacking | Writing to a machine-wide Run key that will later execute in a privileged user's context |

```cmd
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

## 7. Kernel Exploits

Unpatched kernel vulnerabilities can grant direct SYSTEM-level code execution — the "brute force" category of privilege escalation, most effective on systems with a large patch gap (see [[Windows_Update_and_Patch_Management]]).

```powershell
systeminfo                         # gather OS build/patch level for known-vulnerability lookup
Get-HotFix | Sort InstalledOn        # cross-reference against known CVE databases (e.g., via Watson, Sherlock, WES-NG)
```

## 8. Credential-Based Escalation

Directly builds on [[Windows_Authentication]]:

| Technique | Mechanism |
|---|---|
| Credential dumping (LSASS memory) | Extracting cached hashes/tickets from a running LSASS process |
| SAM/SYSTEM hive extraction | Offline extraction and hash cracking of local account credentials |
| Credentials in files/scripts | Passwords hardcoded in scripts, config files, `unattend.xml` (leftover from OS deployment), or Group Policy Preferences XML (see [[Group_Policy]]) |
| Stored/cached credentials | Browser-saved passwords, Windows Credential Manager entries, cached RDP credentials |

```cmd
findstr /si password *.xml *.ini *.txt *.config       :: hunt for hardcoded credentials
cmdkey /list                                              :: list cached Credential Manager entries
```

## 9. UAC Bypass (A Related but Distinct Concept)

As covered in [[UAC_and_Privilege_Levels]], UAC bypass techniques elevate an already-admin-group account from Medium to High integrity — useful in an attack chain, but not equivalent to crossing the standard-user-to-admin boundary itself.

## 10. Enumeration Tooling

Manually checking every vector above is slow — automated enumeration scripts are standard practice in both offensive assessments and defensive hardening reviews:

| Tool | Purpose |
|---|---|
| WinPEAS | Broad automated enumeration across most vectors above |
| Seatbelt | .NET-based situational awareness/enumeration |
| PowerUp (PowerSploit) | PowerShell-based service/permission/registry privesc checks |
| Sysinternals AccessChk | Fine-grained permission auditing on files/registry/services |

## 11. Interview Questions

1. What's the difference between vertical and horizontal privilege escalation? → **Vertical gains a higher privilege level (user → admin → SYSTEM); horizontal gains access to a different account at the same privilege level**
2. What privilege underlies most "Potato"-family local privilege escalation exploits? → **`SeImpersonatePrivilege` (or `SeAssignPrimaryTokenPrivilege`)**, commonly held by service accounts by default
3. What does the AlwaysInstallElevated misconfiguration allow? → **Any user to install an MSI package with SYSTEM privileges, if both the HKLM and HKCU registry values enabling it are set**
4. Why is an unquoted service path with spaces exploitable? → **Windows tries each space-delimited path segment in order as a potential executable, so a writable earlier segment (e.g., `C:\Program.exe`) gets executed instead of the intended binary**
5. What's typically the very first command run when starting a Windows privilege-escalation assessment, and why? → **`whoami /priv`, to immediately see which special privileges the current token already holds — several (SeImpersonate, SeBackup, SeDebug, etc.) directly enable known exploitation techniques**

## 12. Key Points

- Windows privesc draws on nearly every prior topic in this folder: **service misconfigurations, file/registry ACLs, abusable privileges, registry settings, kernel patch level, and credential exposure**.
- `whoami /priv` and checking service/file permissions are the standard first enumeration steps.
- **SeImpersonatePrivilege** (Potato exploits) and **AlwaysInstallElevated** are two of the most commonly-found, easy-to-exploit real-world misconfigurations.
- Automated tools (**WinPEAS, Seatbelt, PowerUp**) systematize checking all these vectors quickly — but understanding what they're actually checking (this note's content) is what lets you interpret and act on their output.

---
*Related: [[Windows_Processes_and_Services]], [[Windows_Registry]], [[UAC_and_Privilege_Levels]], [[Windows_Persistence_and_Lateral_Movement]]*
