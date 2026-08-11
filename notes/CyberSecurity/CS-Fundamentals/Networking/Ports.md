## 1. Introduction

A **port** is a 16-bit number (0-65535) used at the Transport layer (TCP/UDP) to identify a specific process or service on a device, allowing a single IP address to handle many simultaneous connections. If an IP address is like a building's street address, a port is like the specific apartment/room number inside it.

## 2. Port Ranges

| Range | Name | Description |
|---|---|---|
| 0-1023 | Well-Known / System Ports | Assigned by IANA to standard services (HTTP, FTP, SSH, etc.); require admin/root privileges to bind on most OSes |
| 1024-49151 | Registered Ports | Registered with IANA for specific applications (e.g., 3306 MySQL, 8080 alt-HTTP) |
| 49152-65535 | Dynamic / Private / Ephemeral Ports | Temporarily assigned by the OS for outgoing client connections |

## 3. TCP vs UDP Ports

The same port number can be used independently by TCP and UDP — they're separate namespaces. Example: port 53 is used by DNS over both UDP (typical queries) and TCP (zone transfers, large responses).

- **TCP ports** — used by connection-oriented services requiring reliability (web, email, file transfer, remote access).
- **UDP ports** — used by connectionless, low-latency services (DNS queries, DHCP, streaming, VoIP, gaming).

## 4. Common Well-Known Ports

| Port | Protocol | Service |
|---|---|---|
| 20/21 | TCP | FTP (data/control) |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP (server/client) |
| 69 | UDP | TFTP |
| 80 | TCP | HTTP |
| 88 | TCP/UDP | Kerberos |
| 110 | TCP | POP3 |
| 111 | TCP/UDP | RPC |
| 123 | UDP | NTP |
| 135 | TCP | MS RPC |
| 137-139 | TCP/UDP | NetBIOS |
| 143 | TCP | IMAP |
| 161/162 | UDP | SNMP |
| 389 | TCP/UDP | LDAP |
| 443 | TCP | HTTPS |
| 445 | TCP | SMB |
| 465 | TCP | SMTPS |
| 514 | UDP | Syslog |
| 587 | TCP | SMTP (submission) |
| 636 | TCP | LDAPS |
| 993 | TCP | IMAPS |
| 995 | TCP | POP3S |
| 1433 | TCP | MSSQL |
| 1521 | TCP | Oracle DB |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP |
| 5432 | TCP | PostgreSQL |
| 5900 | TCP | VNC |
| 8080 | TCP | HTTP alternate / proxy |
| 8443 | TCP | HTTPS alternate |

## 5. Sockets

A **socket** is the combination of an **IP address + port number + protocol** that uniquely identifies one end of a network connection, e.g. `192.168.1.10:443 (TCP)`. A full TCP connection is uniquely identified by the **5-tuple**: source IP, source port, destination IP, destination port, protocol.

## 6. Port States (from a scanning perspective)

| State | Meaning |
|---|---|
| Open | A service is actively listening and responding |
| Closed | Port is reachable but no service is listening |
| Filtered | A firewall is blocking traffic, so state can't be determined |
| Unfiltered | Port is accessible but state can't be determined without further probing |

## 7. Port Scanning (Security Context)

Port scanning is used by both attackers (reconnaissance) and defenders (auditing) to discover open ports/services on a target.

**Common techniques (e.g., via Nmap):**

| Scan Type | Description |
|---|---|
| TCP Connect Scan | Completes full three-way handshake |
| SYN Scan ("half-open") | Sends SYN, analyzes response, never completes handshake — stealthier |
| UDP Scan | Sends UDP packets, looks for ICMP "port unreachable" to infer closed ports |
| FIN/XMAS/NULL Scan | Uses unusual flag combinations to evade basic firewalls/logging |

**Defensive measures:** firewalls, closing unused ports, IDS/IPS to detect scan patterns, port knocking, rate limiting.

## 8. Practical Commands

```bash
# List listening ports on Linux
ss -tulnp
netstat -tulnp

# List listening ports on Windows
netstat -ano

# Scan a host's ports with Nmap
nmap -sS -p 1-65535 192.168.1.10
```

## 9. Interview Questions

1. What is the range of a port number? → **0-65535**
2. What range are well-known ports in? → **0-1023**
3. What port does HTTPS use? → **443**
4. What is a socket? → **IP address + port + protocol combination**
5. Which scan type never completes the TCP handshake? → **SYN scan (half-open)**
6. What port does DNS commonly use, and over which protocol(s)? → **Port 53, over UDP (queries) and TCP (zone transfers)**

## 10. Key Points

- Ports range from **0-65535**; 0-1023 are well-known, 1024-49151 registered, 49152-65535 ephemeral.
- TCP and UDP maintain separate port namespaces.
- A socket = IP + port + protocol.
- Port scanning is a key reconnaissance and auditing technique — know the scan types.
- Memorize the common ports table — it's a frequent interview and CTF requirement.

---
*Related: [[TCP_Protocol]], [[TCP_IP_Model]], [[DNS]], [[DHCP]]*
