## How to Use This Folder

VAPT is split into 13 specialization folders — **Methodology_and_Standards**, **Reconnaissance_and_OSINT**, **Network_Pentesting**, **Web_Application_Pentesting**, **API_Security_Testing**, **Active_Directory_and_Internal_Pentesting**, **Mobile_Application_Pentesting**, **Wireless_Pentesting**, **Cloud_Pentesting**, **Post_Exploitation_and_Privilege_Escalation**, **Exploit_Development**, **Tools_and_Cheatsheets**, and **Reporting_and_Risk_Scoring** — each with its own `00_Roadmap.md`. This file is the order to move through the folders themselves: building from *how an engagement is run*, through each attack surface in the order a real engagement typically touches them, to *how findings get written up*. This folder assumes CS-Fundamentals is already complete — it leans on Networking, Security Basics ([[Security_Testing_Types]], [[Vulnerability_Management]]), Linux, and Windows throughout without re-explaining them.

**Start with:** Methodology_and_Standards, beginning at [[PTES_Overview]]
**Finish with:** Reporting_and_Risk_Scoring, ending at [[Retesting_and_Closure]]

## Folder 1 — Methodology_and_Standards ✅

**Why first:** before touching a single tool, you need the frameworks that govern *how* an engagement is scoped, authorized, and run — PTES, OSSTMM, the OWASP Testing Guide, NIST SP 800-115 — plus the legal boundary that separates a pentester from an attacker.

→ Start at [[PTES_Overview]], finish at [[MITRE_ATTACK_for_Pentesters]].

## Folder 2 — Reconnaissance_and_OSINT ✅

**Why second:** every real engagement begins here — passive and active information gathering before any exploitation is attempted, exactly where the PTES/OSSTMM lifecycle from Folder 1 says testing starts.

→ Start at [[Passive_vs_Active_Recon]], finish at [[Network_Footprinting]].

## Folder 3 — Network_Pentesting

**Why third:** recon feeds directly into scanning and exploiting the infrastructure layer — ports, services, protocols — the most traditional pentest surface, and a direct extension of the Networking folder in CS-Fundamentals.

## Folder 4 — Web_Application_Pentesting

**Why fourth:** the largest and most commonly-tested attack surface, turning the OWASP Top 10 theory from CS-Fundamentals ([[Web_Application_Attacks_OWASP_Top10]]) into hands-on exploitation.

## Folder 5 — API_Security_Testing

**Why fifth:** a natural extension of web app testing — most modern applications are API-driven, and APIs fail in distinct auth/authorization ways worth their own folder.

## Folder 6 — Active_Directory_and_Internal_Pentesting

**Why sixth:** once external/web surfaces are covered, internal pentesting (assuming an initial foothold) begins — AD attacks build directly on Windows folder concepts (Kerberos, NTLM, SMB, Active_Directory_Basics).

## Folder 7 — Mobile_Application_Pentesting

**Why seventh:** a distinct, largely self-contained specialization (Android/iOS) that still draws on the web app and API testing concepts already covered.

## Folder 8 — Wireless_Pentesting

**Why eighth:** a narrower, physical-proximity specialization, often run as a discrete phase within a broader engagement rather than the main focus.

## Folder 9 — Cloud_Pentesting

**Why ninth:** increasingly central to modern infrastructure — assumes networking, IAM (from Security Basics), and AD/Windows concepts are already in place before layering cloud-specific misconfigurations on top.

## Folder 10 — Post_Exploitation_and_Privilege_Escalation

**Why tenth:** a cross-cutting phase that applies *after* successful exploitation in any of the previous surfaces — deliberately placed after the attack-surface folders so it can reference all of them at once.

## Folder 11 — Exploit_Development

**Why eleventh:** the deepest, most technical specialization — understanding *why* an exploit works at the memory/assembly level, rather than just running one from Folders 3-10.

## Folder 12 — Tools_and_Cheatsheets

**Why twelfth:** a living quick-reference folder, most useful once you already understand the concepts behind each tool from the folders above — reference material, not a learning path.

## Folder 13 — Reporting_and_Risk_Scoring (Capstone)

**Why last:** every engagement, regardless of which surfaces were tested, ends the same way — findings get scored (CVSS), written up, and communicated to both technical and executive audiences.

→ Start at [[CVSS_Scoring_Guide]], finish at [[Retesting_and_Closure]].

## Quick Reference — Folder Order

Methodology_and_Standards → Reconnaissance_and_OSINT → Network_Pentesting → Web_Application_Pentesting → API_Security_Testing → Active_Directory_and_Internal_Pentesting → Mobile_Application_Pentesting → Wireless_Pentesting → Cloud_Pentesting → Post_Exploitation_and_Privilege_Escalation → Exploit_Development → Tools_and_Cheatsheets → Reporting_and_Risk_Scoring

---
*✅ = notes written. Folders 3-13 currently exist as empty subfolders — content to follow. Each completed folder has its own `00_Roadmap.md` with the full note-by-note reading order — open the one inside whichever folder you're currently working through.*
