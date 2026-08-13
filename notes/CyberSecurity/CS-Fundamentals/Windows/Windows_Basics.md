## 1. Introduction

**Windows** is a family of proprietary, graphical operating systems developed by **Microsoft**, first released in 1985 as a GUI shell on top of MS-DOS. Modern Windows (NT-based, since Windows NT 3.1 in 1993) is a fully independent, preemptively multitasked, multi-user-capable OS. It is the dominant desktop OS worldwide and remains the single most-targeted platform in enterprise cybersecurity — which is why understanding it deeply matters as much for defenders as for attackers.

## 2. Windows NT Family — Why "NT" Matters

Everything from Windows XP onward (and Windows Server) is built on the **Windows NT kernel**, a completely different codebase from the old MS-DOS-based Windows 9x line (95/98/ME). NT introduced true preemptive multitasking, a security reference monitor, and a proper kernel/user-mode split — the foundation for [[Windows_Architecture]].

| Era | Examples | Notes |
|---|---|---|
| MS-DOS based | Windows 1.0–ME | 16/32-bit, cooperative multitasking, no real security model |
| Windows NT based | NT 3.1/4.0, 2000, XP, Vista, 7, 8/8.1, 10, 11 | Modern kernel, still in use today |
| Windows Server (NT based) | Server 2003 → Server 2022/2025 | Same NT kernel, tuned for server roles (AD, DNS, file/print) |

## 3. Editions and Purpose

| Edition line | Purpose |
|---|---|
| Home | Consumer desktops/laptops, minimal domain features |
| Pro / Enterprise | Business use — BitLocker, Group Policy, domain join, Hyper-V |
| Server | Runs server roles: Active Directory Domain Services, DNS, DHCP, File/Print, IIS |
| Windows 11 / 10 IoT | Embedded/kiosk/industrial devices |

## 4. Windows vs Linux — Philosophy

| Aspect | Windows | Linux |
|---|---|---|
| Source model | Closed-source, proprietary | Open-source (see [[Linux_Basics]]) |
| Primary interface | GUI-first, CLI secondary (cmd/PowerShell) | CLI-first, GUI optional |
| Config storage | Centralized binary **registry** | Plain-text files (`/etc`) |
| Permissions model | ACL-based (per-object, fine-grained) | rwx bits + optional ACLs |
| Identity/directory service | **Active Directory** | LDAP/Kerberos (often via AD integration or FreeIPA) |
| Package management | MSI/EXE installers, winget, Microsoft Store | apt/dnf/pacman (see [[Package_Management]]) |
| Dominant use case | Enterprise desktops, AD environments | Servers, cloud infrastructure, embedded |

Understanding both isn't optional in security work — most enterprise breaches pivot between a Windows AD environment and Linux-hosted infrastructure at some stage.

## 5. Why Windows Matters for Cybersecurity

- **Enterprise ubiquity** — the vast majority of corporate endpoints and internal servers run Windows, tied together by **Active Directory** (see [[Active_Directory_Basics]]).
- **Attack surface** — largest install base means the most-studied, most-exploited platform; most red-team/pentest engagements against corporate networks eventually touch a Windows domain.
- **Rich telemetry** — Windows Event Logs, Sysmon, and ETW provide deep visibility for defenders (see [[Event_Log_Management]]).
- **Legacy protocols still in production** — NTLM, SMBv1 remnants, and unconstrained delegation are still found in real environments and remain common attack paths.

## 6. Core Concepts You'll Meet Throughout This Folder

| Concept | One-line definition | Covered in |
|---|---|---|
| Kernel/user mode split | Hardware-enforced privilege boundary | [[Windows_Architecture]] |
| Registry | Centralized hierarchical config database | [[Windows_Registry]] |
| NTFS | Windows' primary filesystem | [[NTFS_File_System]] |
| SID | Unique identifier for every account/group | [[Users_Groups_and_SID]] |
| Token | Carries a process/thread's security identity | [[UAC_and_Privilege_Levels]] |
| Active Directory | Centralized directory & authentication service | [[Active_Directory_Basics]] |

## 7. Interview Questions

1. What kernel underlies all modern Windows releases? → **The Windows NT kernel**, introduced with Windows NT 3.1 in 1993
2. What's the fundamental architectural difference between Windows 9x and Windows NT-based systems? → **NT introduced preemptive multitasking, a real kernel/user-mode split, and a security reference monitor; 9x was MS-DOS-based with no real security model**
3. Name two structural differences between how Windows and Linux store configuration. → **Windows centralizes config in the binary registry; Linux uses plain-text files under `/etc`**
4. Why is Active Directory central to enterprise Windows security? → **It's the identity and authentication backbone tying every domain-joined machine and account together — compromising AD often means compromising the whole network**

## 8. Key Points

- All modern Windows (client and Server) runs the **NT kernel** — a single, unified codebase since 1993.
- Windows favors **GUI-first** interaction with ACL-based permissions and a centralized **registry**, contrasting with Linux's CLI-first, file-based config philosophy.
- **Active Directory** is what makes Windows uniquely relevant to enterprise security — most real-world Windows attack chains eventually involve it.
- Legacy protocol support (NTLM, SMB) for backward compatibility is a persistent, real-world source of vulnerabilities.

---
*Related: [[Windows_Architecture]], [[Windows_Boot_Process]], [[Active_Directory_Basics]]*
