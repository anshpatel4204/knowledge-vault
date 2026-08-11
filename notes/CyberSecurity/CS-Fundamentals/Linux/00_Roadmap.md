## How to Use This Folder

This roadmap gives the recommended reading order for the Linux folder — from OS fundamentals and the filesystem, through the shell and scripting, into process/user/system administration, and finally security hardening and privilege escalation as a capstone that ties everything together. Each note links to related ones via `[[wikilinks]]`, but this order avoids forward references to concepts you haven't met yet.

**Start here:** [[Linux_Basics]]
**Finish here:** [[Linux_Privilege_Escalation]]

## Stage 1 — Operating System Fundamentals

What Linux is, how it's structured internally, and how it starts up.

1. [[Linux_Basics]] — history, distributions, philosophy
2. [[Linux_Architecture]] — kernel space vs user space, system calls
3. [[Linux_Boot_Process]] — BIOS/UEFI → GRUB → kernel → init/systemd

## Stage 2 — Filesystem Foundations

Everything is a file — understand the tree before navigating it.

4. [[Linux_File_System_Hierarchy]] — the FHS standard directory layout
5. [[File_Types_and_Inodes]] — the seven file types, inodes, hard/soft links
6. [[File_Permissions]] — rwx, chmod/chown, SUID/SGID/sticky bit

## Stage 3 — Shell & Command Line

The primary interface for everything that follows.

7. [[Linux_Shell_Basics]] — shell vs terminal, bash/zsh/dash
8. [[Essential_Commands]] — navigation, file operations, find
9. [[File_Viewing_and_Editors]] — cat/less/tail, vim, nano
10. [[Text_Processing_Commands]] — grep, sed, awk, cut, sort/uniq
11. [[Regular_Expressions]] — the pattern language behind grep/sed/awk
12. [[Piping_Redirection_and_Operators]] — connecting commands, stdin/stdout/stderr
13. [[Shell_Scripting]] — automating command sequences
14. [[Environment_Variables_and_Shell_Config]] — PATH, .bashrc, and why scripts behave differently than interactive shells

## Stage 4 — Processes & Identity

How Linux runs and manages work, and who's allowed to do what.

15. [[Process_Management]] — ps/top, signals, foreground/background
16. [[Process_Lifecycle_and_IPC]] — fork/exec, zombies/orphans, IPC mechanisms
17. [[User_and_Group_Management]] — /etc/passwd, /etc/shadow, sudo/su

## Stage 5 — System Administration

Installing software, running services, scheduling work, and reading the trail it all leaves behind.

18. [[Package_Management]] — apt/dpkg, dnf/rpm
19. [[Systemd_and_Services]] — systemctl, unit files, targets
20. [[Cron_and_Task_Scheduling]] — recurring and one-time scheduled jobs
21. [[Log_Management]] — /var/log, journalctl, log rotation

## Stage 6 — Storage & Networking Tools

Managing disks and talking to other systems from the command line.

22. [[Disk_and_Storage_Management]] — partitions, filesystems, mounting, LVM
23. [[Archiving_and_Compression]] — tar, gzip, zip
24. [[Linux_Networking_Commands]] — ip/ss, curl/wget, ssh/scp/rsync

## Stage 7 — Security (Capstone)

Applies every prior stage — permissions, users, cron, PATH, services — to hardening a system and to understanding how that same system gets broken into.

25. [[Linux_Security_Hardening]] — SSH hardening, firewalls, SELinux/AppArmor, fail2ban
26. [[Linux_Privilege_Escalation]] — how everything above becomes an attack path when misconfigured

## Quick Reference — Full Order

Linux_Basics → Linux_Architecture → Linux_Boot_Process → Linux_File_System_Hierarchy → File_Types_and_Inodes → File_Permissions → Linux_Shell_Basics → Essential_Commands → File_Viewing_and_Editors → Text_Processing_Commands → Regular_Expressions → Piping_Redirection_and_Operators → Shell_Scripting → Environment_Variables_and_Shell_Config → Process_Management → Process_Lifecycle_and_IPC → User_and_Group_Management → Package_Management → Systemd_and_Services → Cron_and_Task_Scheduling → Log_Management → Disk_and_Storage_Management → Archiving_and_Compression → Linux_Networking_Commands → Linux_Security_Hardening → Linux_Privilege_Escalation
