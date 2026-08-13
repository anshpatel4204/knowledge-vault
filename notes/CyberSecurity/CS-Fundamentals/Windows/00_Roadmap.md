## How to Use This Folder

This roadmap gives the recommended reading order for the Windows folder — from OS fundamentals and boot internals, through the filesystem/registry, the shell, processes/identity, system administration, networking/domain concepts, and finally the security capstone that ties everything together into how Windows systems are actually attacked and defended. Each note links to related ones via `[[wikilinks]]`, but this order avoids forward references to concepts you haven't met yet.

**Start here:** [[Windows_Basics]]
**Finish here:** [[Windows_Persistence_and_Lateral_Movement]]

## Stage 1 — Operating System Fundamentals

What Windows is, how it's structured internally, and how it starts up.

1. [[Windows_Basics]] — history, editions, philosophy vs Linux
2. [[Windows_Architecture]] — kernel mode vs user mode, HAL, Executive, subsystems
3. [[Windows_Boot_Process]] — firmware → Boot Manager → winload → kernel init → logon

## Stage 2 — Filesystem & Registry Foundations

The two structures nearly everything else in Windows sits on top of.

4. [[NTFS_File_System]] — MFT, streams, journaling, volume structure
5. [[File_and_Folder_Permissions]] — NTFS ACLs, inheritance, icacls/takeown
6. [[Windows_Registry]] — hives, keys, values, regedit, persistence relevance

## Stage 3 — Shell & Command Line

The primary interfaces for everything that follows.

7. [[Command_Prompt_Basics]] — cmd.exe, batch basics, core commands
8. [[PowerShell_Basics]] — cmdlets, the object pipeline, providers
9. [[PowerShell_Scripting]] — scripts, modules, execution policy, remoting
10. [[WMI_and_CIM]] — querying and managing Windows internals programmatically

## Stage 4 — Processes, Services & Identity

How Windows runs work, and who's allowed to do what.

11. [[Windows_Processes_and_Services]] — Task Manager, services.msc, svchost.exe
12. [[Users_Groups_and_SID]] — local accounts, SIDs, built-in groups
13. [[Windows_Authentication]] — SAM, LSASS, NTLM, Kerberos
14. [[UAC_and_Privilege_Levels]] — User Account Control, integrity levels, token elevation

## Stage 5 — System Administration

Keeping a system patched, scheduled, logged, and centrally configured.

15. [[Windows_Update_and_Patch_Management]] — WU/WSUS, patch cadence, CVEs
16. [[Task_Scheduler]] — scheduled tasks, triggers, actions
17. [[Event_Log_Management]] — Event Viewer, EVTX, Sysmon, key event IDs
18. [[Group_Policy]] — GPOs, gpedit, gpresult, local vs domain policy

## Stage 6 — Networking & Domain Concepts

How a Windows machine talks to other machines and fits into an enterprise domain.

19. [[Windows_Networking_Commands]] — ipconfig, netstat, netsh, net/PowerShell cmdlets
20. [[SMB_and_Windows_Shares]] — SMB protocol, shares, null sessions, SMB relay
21. [[Active_Directory_Basics]] — domains, OUs, Domain Controllers, LDAP
22. [[Domain_vs_Workgroup]] — trust models, authentication flow, policy scope differences

## Stage 7 — Security (Capstone)

Applies every prior stage — registry, services, tokens, scheduled tasks, SMB, AD — to how Windows is hardened and how that same system gets broken into.

23. [[Windows_Security_Features]] — Defender, Firewall, BitLocker, AppLocker/WDAC, Credential Guard
24. [[Windows_Privilege_Escalation]] — how misconfigurations in earlier stages become attack paths
25. [[Windows_Persistence_and_Lateral_Movement]] — run keys, pass-the-hash, and staying inside a network

## Quick Reference — Full Order

Windows_Basics → Windows_Architecture → Windows_Boot_Process → NTFS_File_System → File_and_Folder_Permissions → Windows_Registry → Command_Prompt_Basics → PowerShell_Basics → PowerShell_Scripting → WMI_and_CIM → Windows_Processes_and_Services → Users_Groups_and_SID → Windows_Authentication → UAC_and_Privilege_Levels → Windows_Update_and_Patch_Management → Task_Scheduler → Event_Log_Management → Group_Policy → Windows_Networking_Commands → SMB_and_Windows_Shares → Active_Directory_Basics → Domain_vs_Workgroup → Windows_Security_Features → Windows_Privilege_Escalation → Windows_Persistence_and_Lateral_Movement
