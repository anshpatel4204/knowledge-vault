## How to Use This Folder

CS-Fundamentals is split into five subject folders — **Networking**, **Security Basics**, **Cryptography**, **Linux**, and **Windows** — each with its own `00_Roadmap.md` giving the exact note-by-note order for that folder. This file is one level up: it's the order to move through the **five folders themselves**, chosen so each folder only assumes vocabulary and concepts the folders before it have already introduced. Once you're inside a folder, switch to that folder's own roadmap for the detailed reading order.

**Start with:** the **Networking** folder, beginning at [[CIA_Triad]]
**Finish with:** the **Windows** folder, ending at [[Windows_Persistence_and_Lateral_Movement]]

## Folder 1 — Networking

**Why first:** everything downstream — the threat landscape, cryptographic protocols, OS-level attacks — assumes you already know how data actually moves between machines (IP, ports, TCP/UDP, DNS, routing/switching). This folder also introduces the **CIA Triad** and **AAA**, the two pieces of vocabulary every later folder leans on without re-explaining.

→ Start at [[CIA_Triad]], finish at [[Wireless_Networking]] — full 26-note order in this folder's own roadmap.

## Folder 2 — Security Basics

**Why second:** builds directly on Networking's vocabulary (DoS, MITM, credential attacks all assume you understand ports/protocols) to cover the broader threat landscape, defensive philosophy, governance, and incident response — the conceptual scaffolding every later technical folder plugs into. For example, [[Windows_Update_and_Patch_Management]] leans directly on this folder's [[Vulnerability_Management]].

→ Start at [[Threat_Actors_and_Hackers]], finish at [[Security_Testing_Types]].

## Folder 3 — Cryptography

**Why third:** once you know what's being defended (Security Basics) and how data travels (Networking), cryptography explains **how** that data and those connections are actually protected — symmetric/asymmetric encryption, hashing, PKI, TLS. The Windows folder's [[Windows_Authentication]] and [[SMB_and_Windows_Shares]] later assume this vocabulary directly (NTLM hashes, Kerberos tickets, encrypted channels).

→ Start at [[Cryptography_Basics]], finish at [[Post_Quantum_Cryptography]].

## Folder 4 — Linux

**Why fourth:** the first of the two OS-specific folders — command line, filesystem, processes, users, and privilege escalation on Linux, the dominant platform for servers and cloud infrastructure. It establishes the OS-level thinking (kernel/user mode, permissions, processes, persistence, privilege escalation) that the Windows folder deliberately mirrors and contrasts against throughout.

→ Start at [[Linux_Basics]], finish at [[Linux_Privilege_Escalation]].

## Folder 5 — Windows (Capstone)

**Why last:** the most enterprise-heavy folder — Active Directory, Group Policy, domain authentication (NTLM/Kerberos) — draws on Networking (SMB, ports), Cryptography (hashing, Kerberos tickets), and Security Basics (vulnerability/patch management) at nearly every turn, and repeatedly contrasts its OS model against Linux's. Its own privilege-escalation and persistence notes are the natural final capstone of the whole CS-Fundamentals folder.

→ Start at [[Windows_Basics]], finish at [[Windows_Persistence_and_Lateral_Movement]].

## Quick Reference — Folder Order

Networking → Security Basics → Cryptography → Linux → Windows

---
*Each folder has its own `00_Roadmap.md` with the full note-by-note reading order — open the one inside whichever folder you're currently working through.*
