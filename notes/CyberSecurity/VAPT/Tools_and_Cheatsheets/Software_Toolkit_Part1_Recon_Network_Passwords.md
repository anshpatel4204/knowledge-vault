## 1. Introduction

This is the first of two notes inventorying the core VAPT software toolkit — not deep command cheatsheets (those come later), but a map of **what each tool does, which folder in this vault it belongs to, and where to actually learn it properly**. Part 1 covers reconnaissance, network scanning/enumeration, and password/credential attack tools. Nearly all of these ship pre-installed on Kali Linux and Parrot OS (see [[Linux_Basics]] in CS-Fundamentals) — installing a pentest distro is the fastest way to get this entire list working at once.

## 2. Reconnaissance & OSINT Tools

| Tool | What It's For | Relevant Folder | Learn It |
|---|---|---|---|
| Nmap | The standard network discovery/port scanner — also does service/version detection and scriptable enumeration via NSE | [[Network_Pentesting]] (Folder 3) | [Nmap Reference Guide](https://nmap.org/book/) |
| theHarvester | Automates email, subdomain, and employee name gathering from multiple public sources | [[Email_Harvesting]], [[OSINT_Framework_and_Tools]] (Folder 2) | [GitHub — laramies/theHarvester](https://github.com/laramies/theHarvester) |
| Amass | In-depth attack surface mapping and subdomain enumeration, combining passive sources with active resolution | [[Subdomain_Enumeration]] (Folder 2) | [OWASP Amass Docs](https://github.com/owasp-amass/amass) |
| Recon-ng | A modular, Metasploit-style reconnaissance framework for OSINT gathering | [[OSINT_Framework_and_Tools]] (Folder 2) | [GitHub Wiki — lanmaster53/recon-ng](https://github.com/lanmaster53/recon-ng/wiki) |

## 3. Network Scanning & Enumeration Tools

| Tool | What It's For | Relevant Folder | Learn It |
|---|---|---|---|
| Wireshark | The standard GUI packet capture/analysis tool — inspecting traffic at the protocol level | [[Network_Pentesting]] (Folder 3), also used throughout CS-Fundamentals Networking | [Wireshark Documentation](https://www.wireshark.org/docs/) |
| Netcat / Ncat | The "Swiss Army knife" of networking — port scanning, banner grabbing, file transfer, reverse/bind shells | [[Network_Pentesting]] (Folder 3), [[Post_Exploitation_and_Privilege_Escalation]] (Folder 10) | [Ncat Reference Guide](https://nmap.org/ncat/) |
| Nikto | Automated web server scanner checking for outdated software, dangerous files, and common misconfigurations | [[Network_Pentesting]] (Folder 3), [[Web_Application_Pentesting]] (Folder 4) | [GitHub Wiki — sullo/nikto](https://github.com/sullo/nikto/wiki) |
| Responder | LLMNR/NBT-NS/mDNS poisoner — captures credential hashes from Windows name-resolution broadcast traffic | [[Active_Directory_and_Internal_Pentesting]] (Folder 6) | [GitHub — lgandx/Responder](https://github.com/lgandx/Responder) |

## 4. Password & Credential Attack Tools

| Tool | What It's For | Relevant Folder | Learn It |
|---|---|---|---|
| John the Ripper | The classic offline password cracker — supports a huge range of hash formats | [[Post_Exploitation_and_Privilege_Escalation]] (Folder 10) | [Openwall John Docs](https://www.openwall.com/john/doc/) |
| Hashcat | GPU-accelerated password cracking — significantly faster than CPU-based tools like John for most hash types | [[Post_Exploitation_and_Privilege_Escalation]] (Folder 10) | [Hashcat Wiki](https://hashcat.net/wiki/) |
| Hydra (THC-Hydra) | Fast online (network-service) login brute-forcer — SSH, FTP, HTTP forms, RDP, and dozens more protocols | [[Network_Pentesting]] (Folder 3) | [GitHub — vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra) |
| NetExec (formerly CrackMapExec) | Swiss-army-knife tool for enumerating and attacking Active Directory/SMB environments at scale | [[Active_Directory_and_Internal_Pentesting]] (Folder 6) | [NetExec Documentation](https://www.netexec.wiki/) |
| Medusa | A fast, modular, parallel online login brute-forcer, similar in purpose to Hydra | [[Network_Pentesting]] (Folder 3) | [GitHub — jmk-foofus/medusa](https://github.com/jmk-foofus/medusa) |

## 5. A Note on Learning Order

Don't try to learn every tool in this list at once. The practical order matches the folder progression: get comfortable with **Nmap and Wireshark** first (they underpin nearly everything else), then layer in the OSINT tools while working through Folder 2, then the password tools once Folder 3/6 techniques give you actual hashes/credentials to practice against.

## 6. Interview Questions

1. What's the core difference between Hydra and John the Ripper? → **Hydra performs online brute-forcing against live network services/login forms; John the Ripper cracks password hashes offline, once they've already been obtained**
2. Why has NetExec become the recommended tool over CrackMapExec? → **CrackMapExec's original development was discontinued/archived; NetExec is the actively maintained community fork carrying the same core functionality forward**
3. What does Responder actually capture, and how? → **It poisons LLMNR/NBT-NS/mDNS name-resolution broadcast traffic on a network, tricking misconfigured Windows hosts into sending their credential hashes (typically NTLMv2) to the attacker's machine**
4. Why is Hashcat generally faster than John the Ripper for the same hash-cracking task? → **Hashcat is built for GPU acceleration, which massively parallelizes the cracking workload compared to John's traditionally CPU-based approach (though John has since added GPU support too)**

## 7. Key Points

- Nearly this entire toolkit ships **pre-installed on Kali Linux/Parrot OS** — a pentest distro is the fastest path to a working environment.
- **Nmap and Wireshark** are the foundational tools nearly every other technique in this vault assumes familiarity with.
- **Online brute-forcing** (Hydra, Medusa — against a live service) is a fundamentally different technique from **offline cracking** (John, Hashcat — against an already-obtained hash).
- Continue to [[Software_Toolkit_Part2_Exploitation_Web_AD_Wireless]] for exploitation frameworks, web app testing, AD, and wireless/mobile/cloud tools.

---
*Related: [[00_Roadmap]] (this folder), [[Network_Pentesting]], [[Reconnaissance_and_OSINT]], [[Active_Directory_and_Internal_Pentesting]]*
