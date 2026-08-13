## How to Use This File

This is a reference index of the tools and software used for **networking work in cybersecurity** — scanning, packet analysis, wireless, IDS/IPS, firewalls, lab building, monitoring, and reconnaissance. It sits at the CS-Fundamentals level (not inside a single subfolder) because these tools cut across the Networking, Linux, Windows, and Security Basics folders.

Each entry gives: what the tool does, which platform it runs on, and a **link to learn it**. Every link below was checked and is either the tool's official documentation or a recognised free learning resource.

**Read the concepts first, then the tools.** A tool only makes sense once you know what it's manipulating — run [[00_Roadmap]]'s folder order (Networking → Security Basics → Cryptography → Linux → Windows) alongside this file rather than instead of it.

**Note on the Kali links:** `kali.org/tools/<name>/` is the official Kali package documentation page for each tool — a consistent, reliable per-tool reference for anything packaged in Kali. It's used below wherever a tool has no better standalone doc site.

## 1. Base Platform — Where You Run Everything

Before individual tools, you need an OS to run them from. Almost all offensive networking tooling assumes Linux.

| Software | What it is | Link |
|---|---|---|
| **Kali Linux** | The standard penetration-testing distribution — ships with nearly every tool in this file pre-installed | [kali.org/tools](https://www.kali.org/tools/) |
| **Parrot Security OS** | Alternative pentest distro, lighter than Kali, privacy-focused | [parrotsec.org](https://www.parrotsec.org/) |
| **Ubuntu / Debian** | The realistic server-side counterpart — what you'll actually be defending | [ubuntu.com/server/docs](https://ubuntu.com/server/docs) |
| **Windows + WSL** | Run Linux tooling on a Windows host; also needed for Windows-native tools (see [[Windows_Networking_Commands]]) | [learn.microsoft.com/windows/wsl](https://learn.microsoft.com/en-us/windows/wsl/) |

**Recommendation:** run Kali in a VM (VirtualBox/VMware), never as your daily driver — you want snapshots and network isolation.

## 2. Network Discovery & Port Scanning

Finding what hosts exist and what services they expose — the first active step in almost any assessment. Builds directly on [[Ports]], [[IP_Address]], and [[TCP_Protocol]].

| Tool | Purpose | Platform | Learn it |
|---|---|---|---|
| **Nmap** | The definitive port scanner — host discovery, service/version detection, OS fingerprinting, NSE scripting | Linux/Win/mac | [Nmap Network Scanning (full book, free online)](https://nmap.org/book/) · [Reference Guide](https://nmap.org/book/man.html) |
| **Zenmap** | Nmap's official GUI, useful while learning to see command construction | Linux/Win/mac | [nmap.org/zenmap](https://nmap.org/zenmap/) |
| **Masscan** | Internet-scale port scanner — far faster than Nmap, far less detail | Linux | [kali.org/tools/masscan](https://www.kali.org/tools/masscan/) |
| **Netdiscover** | Passive/active ARP-based host discovery on a local segment (see [[ARP]]) | Linux | [kali.org/tools/netdiscover](https://www.kali.org/tools/netdiscover/) |
| **arp-scan** | Fast layer-2 host discovery via ARP requests | Linux | [kali.org/tools/arp-scan](https://www.kali.org/tools/arp-scan/) |
| **Angry IP Scanner** | Lightweight cross-platform GUI scanner, good for quick sweeps | Linux/Win/mac | [angryip.org](https://angryip.org/) |

**Start with Nmap and go deep.** The Nmap book is genuinely one of the best free networking resources that exists — the scanning theory chapters teach TCP/IP behaviour, not just flags.

```bash
nmap -sS -sV -O -p- 192.168.1.0/24        # SYN scan, version + OS detection, all ports
nmap -sn 192.168.1.0/24                    # host discovery only ("ping sweep")
nmap --script vuln 10.10.10.5              # NSE vulnerability scripts
```

## 3. Packet Capture & Traffic Analysis

Reading what is actually on the wire — the single most transferable skill in network security. Directly applies [[OSI_MODEL]], [[TCP_Protocol]], [[UDP_Protocol]], and [[DNS]].

| Tool | Purpose | Platform | Learn it |
|---|---|---|---|
| **Wireshark** | The GUI packet analyser — capture, dissect, filter, follow streams, decode hundreds of protocols | Linux/Win/mac | [Official User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/) · [All docs](https://www.wireshark.org/docs/) |
| **tshark** | Wireshark's CLI version — scriptable, essential for automation and remote captures | Linux/Win/mac | [tshark man page](https://www.wireshark.org/docs/man-pages/tshark.html) |
| **tcpdump** | The lightweight CLI capture standard, present on virtually every Linux/Unix host | Linux/mac | [Official man page](https://www.tcpdump.org/manpages/tcpdump.1.html) |
| **Termshark** | Terminal UI over tcpdump captures — Wireshark-like filtering without a GUI | Linux | [kali.org/tools/termshark](https://www.kali.org/tools/termshark/) |
| **NetworkMiner** | Network forensic analysis — extracts files, credentials, and hosts from PCAPs | Windows/Linux | [netresec.com/?page=NetworkMiner](https://www.netresec.com/?page=NetworkMiner) |

**Practice material:** the best way to learn Wireshark is analysing real malicious traffic, not your own browsing.

- [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net/) — free real-world PCAP exercises with answers
- [Wireshark sample captures wiki](https://wiki.wireshark.org/SampleCaptures) — protocol-by-protocol example PCAPs

```bash
tcpdump -i eth0 -nn -s0 -w capture.pcap        # capture full packets to file
tcpdump -i eth0 'tcp port 80 and host 10.0.0.5' # BPF filter syntax
tshark -r capture.pcap -Y "http.request" -T fields -e http.host
```

## 4. Built-In Network Diagnostics

Not glamorous, but these are what you'll actually use daily — and what's available on a host where you can't install anything.

| Tool | Purpose | Platform | Learn it |
|---|---|---|---|
| **ping / traceroute / tracert** | Reachability and path tracing via [[ICMP]] | All | [[Windows_Networking_Commands]] · [[Linux_Networking_Commands]] |
| **ip / ifconfig / ipconfig** | Interface and addressing configuration | Linux / Win | [kali.org/tools/iproute2](https://www.kali.org/tools/iproute2/) |
| **ss / netstat** | Listening ports and active connections | All | [kali.org/tools/iproute2](https://www.kali.org/tools/iproute2/) |
| **dig / nslookup / host** | DNS resolution and record queries (see [[DNS]]) | All | [kali.org/tools/bind9](https://www.kali.org/tools/bind9/) |
| **mtr** | Continuous traceroute + packet loss per hop — better than either ping or traceroute alone | Linux/Win/mac | [kali.org/tools/mtr](https://www.kali.org/tools/mtr/) |
| **netcat (nc) / ncat** | The "TCP/IP Swiss army knife" — arbitrary connections, listeners, file transfer, banner grabbing | All | [Ncat guide (Nmap project)](https://nmap.org/ncat/guide/index.html) |
| **socat** | Netcat's more powerful relative — bidirectional relays, TLS, sockets, tunnels | Linux | [kali.org/tools/socat](https://www.kali.org/tools/socat/) |
| **iperf3** | Bandwidth/throughput measurement between two endpoints | All | [iperf.fr](https://iperf.fr/) |

## 5. Traffic Generation & Manipulation

Crafting packets by hand — how you actually test firewall rules and understand protocol behaviour rather than reading about it.

| Tool | Purpose | Platform | Learn it |
|---|---|---|---|
| **hping3** | Custom TCP/UDP/ICMP packet crafting — firewall rule testing, spoofed scans, path MTU discovery | Linux | [kali.org/tools/hping3](https://www.kali.org/tools/hping3/) |
| **Scapy** | Python packet crafting/manipulation library — build and send any packet programmatically | Linux/Win/mac | [scapy.readthedocs.io](https://scapy.readthedocs.io/en/latest/) |
| **Ettercap** | Classic MITM framework — ARP poisoning, traffic interception on a LAN | Linux | [kali.org/tools/ettercap](https://www.kali.org/tools/ettercap/) |
| **Bettercap** | Modern MITM/network attack framework — successor in spirit to Ettercap, covers Wi-Fi/BLE too | Linux | [bettercap.org](https://www.bettercap.org/) |
| **Responder** | LLMNR/NBT-NS/mDNS poisoner — captures credentials on Windows networks (see [[SMB_and_Windows_Shares]]) | Linux | [kali.org/tools/responder](https://www.kali.org/tools/responder/) |

**Scapy is worth the time.** Writing a TCP handshake packet-by-packet in Scapy teaches [[TCP_Protocol]] better than any diagram.

## 6. Wireless Networking

Applies [[Wireless_Networking]] — requires a wireless adapter that supports monitor mode and packet injection (not all do; this is the usual beginner blocker).

| Tool | Purpose | Platform | Learn it |
|---|---|---|---|
| **Aircrack-ng suite** | The standard Wi-Fi auditing toolkit — capture, deauth, WEP/WPA handshake cracking | Linux | [Official documentation](https://www.aircrack-ng.org/documentation.html) |
| **Kismet** | Passive wireless detection, sniffing, and IDS — finds hidden networks | Linux | [kismetwireless.net/docs](https://www.kismetwireless.net/docs/) |
| **Wifite** | Automated wrapper around aircrack-ng and friends — good for seeing the workflow end-to-end | Linux | [kali.org/tools/wifite](https://www.kali.org/tools/wifite/) |
| **hcxdumptool / hcxtools** | Modern WPA/WPA2 (PMKID) capture tooling | Linux | [kali.org/tools/hcxtools](https://www.kali.org/tools/hcxtools/) |

**Legal note:** capturing or attacking wireless networks you don't own is illegal in essentially every jurisdiction. Build your own test AP.

## 7. Vulnerability Scanning

Finding known weaknesses at scale — the operational half of [[Vulnerability_Management]] and [[Windows_Update_and_Patch_Management]].

| Tool | Purpose | Cost | Learn it |
|---|---|---|---|
| **OpenVAS / Greenbone** | Full open-source vulnerability scanner — the standard free option | Free | [Greenbone Community Docs](https://greenbone.github.io/docs/latest/) · [openvas.org](https://openvas.org/) |
| **Nessus Essentials** | Industry-standard commercial scanner; free tier covers 16 IPs | Free tier | [tenable.com/products/nessus](https://www.tenable.com/products/nessus) |
| **Qualys** | Enterprise cloud-based vulnerability management platform | Commercial | [qualys.com](https://www.qualys.com/) |
| **Nikto** | Fast web server vulnerability scanner — noisy but useful for quick coverage | Free | [kali.org/tools/nikto](https://www.kali.org/tools/nikto/) |
| **testssl.sh / sslscan** | TLS/SSL configuration auditing — ciphers, protocols, certificate issues (see [[TLS_SSL]]) | Free | [testssl.sh](https://testssl.sh/) · [kali.org/tools/sslscan](https://www.kali.org/tools/sslscan/) |

## 8. Exploitation & Network Post-Exploitation

Where network access becomes system access. Ties into [[Windows_Privilege_Escalation]] and [[Windows_Persistence_and_Lateral_Movement]].

| Tool | Purpose | Learn it |
|---|---|---|
| **Metasploit Framework** | The exploitation framework — exploits, payloads, post-modules, pivoting | [docs.metasploit.com](https://docs.metasploit.com/) |
| **Impacket** | Python library + scripts for raw Windows network protocols (SMB, MSRPC, Kerberos) — `psexec.py`, `secretsdump.py`, `GetUserSPNs.py` | [github.com/fortra/impacket](https://github.com/fortra/impacket) |
| **NetExec (formerly CrackMapExec)** | Automates assessing large Windows/AD networks over SMB/WinRM/LDAP | [netexec.wiki](https://www.netexec.wiki/) |
| **BloodHound** | Graphs Active Directory attack paths — surfaces non-obvious privilege chains (see [[Active_Directory_Basics]]) | [bloodhound.readthedocs.io](https://bloodhound.readthedocs.io/) |
| **Proxychains** | Forces any tool through a SOCKS/HTTP proxy — how you pivot tooling through a foothold | [kali.org/tools/proxychains-ng](https://www.kali.org/tools/proxychains-ng/) |

## 9. IDS / IPS & Network Security Monitoring

The defensive side — detecting the activity every tool above generates. Pairs with [[Event_Log_Management]].

| Tool | Purpose | Learn it |
|---|---|---|
| **Zeek** (formerly Bro) | Network traffic analysis framework — produces rich structured logs rather than just alerts | [docs.zeek.org](https://docs.zeek.org/) |
| **Suricata** | High-performance IDS/IPS/NSM with multi-threading and rule-based detection | [docs.suricata.io](https://docs.suricata.io/) |
| **Snort** | The original open-source IDS/IPS, maintained by Cisco Talos | [snort.org/documents](https://www.snort.org/documents) |
| **Security Onion** | Complete NSM distribution bundling Zeek, Suricata, Elastic, and hunting tooling | [docs.securityonion.net](https://docs.securityonion.net/) |
| **Arkime** (formerly Moloch) | Full packet capture indexing and search at scale | [arkime.com](https://arkime.com/) |

**Suggested path:** Suricata for rule-based alerting, Zeek for behavioural logging, Security Onion to see them working together in one deployable stack.

## 10. Firewalls & Network Infrastructure

Applies [[Firewalls]], [[NAT]], [[VLAN]], and [[VPN]] to real, configurable systems.

| Software | Purpose | Learn it |
|---|---|---|
| **pfSense** | Full open-source firewall/router OS — firewall rules, NAT, VPN, VLANs, traffic shaping | [docs.netgate.com/pfsense](https://docs.netgate.com/pfsense/en/latest/) |
| **OPNsense** | pfSense fork with a different UI/release model | [docs.opnsense.org](https://docs.opnsense.org/) |
| **iptables / nftables** | Linux's native packet filtering (see [[Linux_Security_Hardening]]) | [netfilter.org/documentation](https://www.netfilter.org/documentation/) |
| **ufw** | Simplified frontend to iptables — the practical everyday Linux firewall | [kali.org/tools/ufw](https://www.kali.org/tools/ufw/) |
| **Windows Defender Firewall** | Host firewall on Windows (see [[Windows_Security_Features]]) | [[Windows_Networking_Commands]] |
| **WireGuard / OpenVPN** | Modern and classic VPN implementations | [wireguard.com](https://www.wireguard.com/) · [openvpn.net/community-resources](https://openvpn.net/community-resources/) |

## 11. Network Simulation & Lab Building

You cannot learn networking without a lab. These let you build multi-router, multi-VLAN topologies without buying hardware.

| Tool | Purpose | Cost | Learn it |
|---|---|---|---|
| **Cisco Packet Tracer** | Beginner-friendly network simulator — routing, switching, VLANs, ACLs | Free | [netacad.com/courses/packet-tracer](https://www.netacad.com/courses/packet-tracer) |
| **GNS3** | Full network emulator running real device images — far more realistic than Packet Tracer | Free | [docs.gns3.com](https://docs.gns3.com/) |
| **EVE-NG** | Enterprise-grade network emulation platform | Free/Pro | [eve-ng.net](https://www.eve-ng.net/) |
| **VirtualBox / VMware Workstation** | The VMs your lab hosts run on | Free/Paid | [virtualbox.org/manual](https://www.virtualbox.org/manual/) |
| **Docker** | Lightweight containerised services for practice targets | Free | [docs.docker.com](https://docs.docker.com/) |

**Minimum viable home lab:** VirtualBox + Kali VM + one vulnerable target VM (Metasploitable, DVWA) on a host-only network. Add pfSense as a gateway VM once basics are comfortable.

## 12. Proxies & Web Traffic Interception

Sitting between browser and server — where web security work actually happens.

| Tool | Purpose | Cost | Learn it |
|---|---|---|---|
| **Burp Suite Community** | The standard web proxy — intercept, modify, and replay HTTP(S) requests | Free tier | [portswigger.net/burp/documentation](https://portswigger.net/burp/documentation) |
| **OWASP ZAP** | Fully free, open-source alternative to Burp with active scanning | Free | [zaproxy.org/docs](https://www.zaproxy.org/docs/) |
| **mitmproxy** | CLI/scriptable intercepting proxy — excellent for automation and mobile traffic | Free | [docs.mitmproxy.org](https://docs.mitmproxy.org/stable/) |

**Learn Burp here:** [PortSwigger Web Security Academy](https://portswigger.net/web-security) — free, interactive labs, made by Burp's own authors. This is the single best free web security resource available.

## 13. SIEM, Logging & Monitoring

Aggregating and searching what everything above produces.

| Tool | Purpose | Cost | Learn it |
|---|---|---|---|
| **Splunk** | Market-leading SIEM/log platform; free tier for small volumes | Free tier | [docs.splunk.com](https://docs.splunk.com/) |
| **Elastic Stack (ELK)** | Elasticsearch + Logstash + Kibana — the open-source logging/search standard | Free | [elastic.co/guide](https://www.elastic.co/guide/index.html) |
| **Wazuh** | Open-source XDR/SIEM with host agents | Free | [documentation.wazuh.com](https://documentation.wazuh.com/) |
| **Zabbix** | Infrastructure and network monitoring | Free | [zabbix.com/documentation](https://www.zabbix.com/documentation/current/en) |
| **Nagios Core** | Long-standing network/host monitoring system | Free | [nagios.org/documentation](https://www.nagios.org/documentation/) |
| **Grafana** | Visualisation layer over metrics/log backends | Free | [grafana.com/docs](https://grafana.com/docs/) |

## 14. OSINT & Passive Reconnaissance

Information gathering that touches no target system directly — usually the legal, low-risk first phase.

| Tool | Purpose | Learn it |
|---|---|---|
| **Shodan** | Search engine for internet-exposed devices and services | [help.shodan.io](https://help.shodan.io/) |
| **Censys** | Similar internet-wide asset/certificate search | [docs.censys.com](https://docs.censys.com/) |
| **theHarvester** | Collects emails, subdomains, and hosts from public sources | [kali.org/tools/theharvester](https://www.kali.org/tools/theharvester/) |
| **Amass** | In-depth attack surface mapping and subdomain enumeration (OWASP project) | [kali.org/tools/amass](https://www.kali.org/tools/amass/) |
| **Maltego** | Graphical link analysis for OSINT relationships | [maltego.com/support](https://www.maltego.com/support/) |
| **Recon-ng** | Modular reconnaissance framework with a Metasploit-like interface | [kali.org/tools/recon-ng](https://www.kali.org/tools/recon-ng/) |

## 15. Free Platforms to Actually Practise On

Tool documentation teaches syntax. These teach application — and they're all free or have substantial free tiers.

| Platform | What it gives you | Link |
|---|---|---|
| **TryHackMe** | Guided, beginner-friendly rooms with the lab environment provided; strong networking and Windows/AD paths | [tryhackme.com](https://tryhackme.com/) |
| **Hack The Box Academy** | Structured modules with hands-on labs; steeper but more rigorous than THM | [academy.hackthebox.com](https://academy.hackthebox.com/) |
| **PortSwigger Web Security Academy** | Free, complete web security curriculum with interactive labs | [portswigger.net/web-security](https://portswigger.net/web-security) |
| **malware-traffic-analysis.net** | Real malicious PCAPs with exercise questions and answers | [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net/) |
| **OverTheWire (Bandit)** | Linux/CLI fundamentals via wargames — do this before anything else if the shell is unfamiliar | [overthewire.org/wargames/bandit](https://overthewire.org/wargames/bandit/) |
| **Cisco Networking Academy** | Formal networking courses, including free introductory tracks | [netacad.com](https://www.netacad.com/) |

## 16. Suggested Learning Order

Don't install everything at once. This order builds each tool on the concepts and tools before it:

```
1. Linux CLI fundamentals        → OverTheWire Bandit
2. Built-in diagnostics          → ping, dig, ip, ss, netcat
3. Packet analysis               → Wireshark + tcpdump on your own traffic
4. Real traffic analysis         → malware-traffic-analysis.net PCAPs
5. Scanning                      → Nmap (read the book, not just the flags)
6. Lab building                  → VirtualBox + Kali + a vulnerable target VM
7. Packet crafting               → Scapy, hping3
8. Web interception              → Burp / ZAP + Web Security Academy
9. Vulnerability scanning        → OpenVAS / Nessus Essentials
10. Exploitation                 → Metasploit, Impacket, NetExec
11. Windows/AD networking        → BloodHound, Responder (see the Windows folder)
12. Defensive side               → Suricata, Zeek, Security Onion
13. Infrastructure               → pfSense, GNS3 topologies
14. Aggregation                  → Elastic / Splunk / Wazuh
```

## 17. Legal and Ethical Boundary

Every tool in sections 2, 5, 6, 7, and 8 is capable of causing real disruption and is illegal to use against systems you do not own or have **explicit written authorisation** to test. This is not a formality — unauthorised scanning alone is a criminal offence in many jurisdictions, including under India's IT Act, the US CFAA, and the UK Computer Misuse Act.

**Rule of thumb:** if you can't point to the specific document authorising you to test that specific host, you're not authorised. Use your own lab (section 11) or the practice platforms (section 15).

## 18. Key Points

- **Tools follow concepts, not the reverse** — Nmap output is meaningless without [[Ports]] and [[TCP_Protocol]]; Wireshark is noise without [[OSI_MODEL]].
- The four tools worth genuine depth early on: **Nmap**, **Wireshark**, **tcpdump**, and **Burp/ZAP** — nearly everything else is situational.
- **Kali** bundles almost all of this pre-installed; `kali.org/tools/<name>/` documents each package individually.
- Free, high-quality primary sources exist for nearly every tool here — the **Nmap book**, **Wireshark User's Guide**, and **PortSwigger Web Security Academy** in particular are better than most paid courses.
- **Build a lab before installing offensive tooling.** Practising against systems you don't own is a criminal offence, not an ethics footnote.

---
*Related: [[00_Roadmap]], [[Ports]], [[TCP_Protocol]], [[Firewalls]], [[Wireless_Networking]], [[Windows_Networking_Commands]], [[Linux_Networking_Commands]], [[Vulnerability_Management]], [[Security_Testing_Types]]*
