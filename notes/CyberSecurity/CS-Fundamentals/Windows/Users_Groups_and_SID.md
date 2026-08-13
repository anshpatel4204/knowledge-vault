## 1. Introduction

Every security decision in Windows — file access, service rights, group policy application — ultimately traces back to **who** is asking, represented not by a username but by a **Security Identifier (SID)**. Understanding accounts, groups, and SIDs is the foundation for everything covered in [[Windows_Authentication]] and [[UAC_and_Privilege_Levels]].

## 2. Security Identifiers (SIDs)

A **SID** is a unique, immutable value identifying a security principal (user, group, computer, or domain) — unlike a username, it never changes even if the account is renamed, and it's never reused after an account is deleted.

```
S-1-5-21-3623811015-3361044348-30300820-1013
│  │ │  └────────────┬────────────┘ └─┬─┘
│  │ │        Domain/machine ID    RID (Relative ID)
│  │ └─ Authority (5 = NT Authority)
│  └─ Revision level
└─ Literal "S" prefix
```

| SID | Represents |
|---|---|
| `S-1-1-0` | Everyone |
| `S-1-5-18` | LocalSystem |
| `S-1-5-19` | LocalService |
| `S-1-5-20` | NetworkService |
| `S-1-5-32-544` | Built-in Administrators group |
| `S-1-5-32-545` | Built-in Users group |
| `S-1-5-21-...-500` | The built-in local/domain Administrator account (RID 500 is always Administrator) |
| `S-1-5-21-...-501` | The built-in Guest account (RID 501) |

**Why RID 500 matters:** because the built-in Administrator's RID is always `500` regardless of language/rename, attackers and defenders alike use this fact — renaming the account doesn't hide it from SID-based enumeration.

```cmd
whoami /user                :: current user's SID
wmic useraccount get name,sid    :: local accounts and their SIDs
```

```powershell
Get-LocalUser | Select-Object Name, SID
[System.Security.Principal.NTAccount]"Ansh" | ` 
    Translate([System.Security.Principal.SecurityIdentifier])   # username → SID
```

## 3. Local User Accounts

```cmd
net user                          :: list local users
net user Ansh                        :: view details on one user
net user Ansh Passw0rd! /add            :: create a local user
net user Ansh /delete                      :: delete a local user
net user Ansh /active:no                      :: disable an account
```

```powershell
Get-LocalUser
New-LocalUser -Name "Ansh" -Password (ConvertTo-SecureString "Passw0rd!" -AsPlainText -Force)
Remove-LocalUser -Name "Ansh"
Disable-LocalUser -Name "Ansh"
```

## 4. Built-In Accounts

| Account | Purpose |
|---|---|
| Administrator | Highest local privilege; disabled by default on modern client Windows in favor of UAC-elevated admin accounts |
| Guest | Minimal-privilege, disabled by default |
| LocalSystem (SYSTEM) | Used by the OS/kernel-mode components and most services — effectively unrestricted local access, but **no** network credentials of its own |
| LocalService | Runs services with minimal local privileges and anonymous network access |
| NetworkService | Runs services with minimal local privileges but presents the computer's network identity |

**SYSTEM vs Administrator:** SYSTEM actually has *more* local privilege than an Administrator account in several respects (e.g., access to protected OS files/registry areas Administrators can't touch by default), which is exactly why privilege escalation to SYSTEM — not just "Administrator" — is the typical end-goal in Windows exploitation.

## 5. Groups — Local and Built-In

```cmd
net localgroup                        :: list local groups
net localgroup Administrators            :: list members of a group
net localgroup Administrators Ansh /add     :: add a user to a group
```

```powershell
Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"
Add-LocalGroupMember -Group "Administrators" -Member "Ansh"
```

| Built-in group | Grants |
|---|---|
| Administrators | Full local control |
| Users | Standard, non-privileged access |
| Backup Operators | Can bypass file permissions specifically for backup/restore — a known privesc path via `SeBackupPrivilege`/`SeRestorePrivilege` |
| Remote Desktop Users | Permitted to log on via RDP |
| Power Users | Legacy, largely deprecated elevated group |

## 6. User Rights vs Permissions — An Important Distinction

| Concept | Scope | Example |
|---|---|---|
| **Permission** | Applies to a specific object (file, key, service) via its ACL | "Ansh can Read `C:\Data`" |
| **User Right / Privilege** | Applies system-wide, granted via Local Security Policy, independent of any object | "Ansh can log on locally," "Ansh can debug programs" (`SeDebugPrivilege`) |

```cmd
secpol.msc                       :: Local Security Policy GUI — manage user rights
whoami /priv                        :: list current session's assigned privileges
```

Privileges like `SeDebugPrivilege`, `SeBackupPrivilege`, `SeImpersonatePrivilege`, and `SeLoadDriverPrivilege` are frequently abused in privilege-escalation chains when assigned more broadly than necessary — see [[Windows_Privilege_Escalation]].

## 7. Interview Questions

1. What is a SID, and how is it different from a username? → **A unique, immutable numeric identifier for a security principal; unlike a username, it never changes and is never reused, even across renames or account deletion**
2. What RID does the built-in Administrator account always have? → **500 — regardless of renaming or localization**
3. What's the practical difference between SYSTEM and a local Administrator account? → **SYSTEM has broader local access to protected OS resources than even an Administrator; it has no network credentials of its own, whereas Administrator does**
4. What's the difference between a "permission" and a "user right" in Windows terminology? → **Permissions apply to a specific object via its ACL; user rights/privileges are system-wide capabilities (e.g., SeDebugPrivilege) granted via Local Security Policy, independent of any single object**
5. Why is the Backup Operators group a notable privilege-escalation path? → **Membership grants SeBackupPrivilege/SeRestorePrivilege, which let a user read/write any file on the system regardless of its ACL — bypassable to read sensitive files like SAM/SYSTEM hives**

## 8. Key Points

- Every security principal has a unique **SID**; the built-in Administrator account is always RID **500**.
- **SYSTEM**, not just Administrator, is the typical real target of privilege escalation, since it holds broader local access.
- **Permissions** (object-level, via ACLs) and **user rights/privileges** (system-wide, via Local Security Policy) are distinct concepts — both matter for privilege escalation analysis.
- Certain built-in groups (**Backup Operators** especially) grant privileges that bypass normal file ACLs entirely — a well-known escalation path.

---
*Related: [[Windows_Authentication]], [[UAC_and_Privilege_Levels]], [[File_and_Folder_Permissions]], [[Windows_Privilege_Escalation]]*
