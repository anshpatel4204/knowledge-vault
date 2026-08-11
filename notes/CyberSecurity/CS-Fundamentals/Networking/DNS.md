## 1. Introduction

**DNS (Domain Name System)** translates human-readable domain names (e.g., `google.com`) into machine-readable IP addresses (e.g., `142.250.183.14`). It is often called the "phone book of the internet" and operates primarily over **UDP port 53** (queries) and **TCP port 53** (zone transfers, large/DNSSEC responses).

## 2. Why DNS Exists

Computers route traffic using IP addresses, but humans remember names far better than numbers. DNS provides that translation layer, decoupling human-facing names from the underlying (and potentially changing) IP infrastructure.

## 3. DNS Hierarchy

DNS is a distributed, hierarchical, tree-like database:

```
                    . (Root)
                     |
        -----------------------------
        |          |          |
       .com       .org       .net   (TLDs)
        |
     google.com   (Second-Level Domain)
        |
   www.google.com  (Subdomain / FQDN)
```

| Level | Example | Description |
|---|---|---|
| Root | `.` | Managed by 13 root server clusters worldwide |
| TLD (Top-Level Domain) | `.com`, `.org`, `.in` | Managed by registries (Verisign, etc.) |
| Second-Level Domain | `google.com` | Registered by an organization |
| Subdomain | `mail.google.com` | Created by the domain owner |

## 4. DNS Record Types

| Record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| CNAME | Alias — maps a name to another name |
| MX | Mail exchange — specifies mail servers for a domain |
| NS | Nameserver — specifies authoritative DNS servers for a domain |
| PTR | Pointer — used for reverse DNS lookups (IP → name) |
| TXT | Free-text records, often used for SPF, DKIM, domain verification |
| SOA | Start of Authority — administrative info about a zone (serial, refresh, TTL) |
| SRV | Specifies location of specific services (e.g., SIP, LDAP) |
| CAA | Specifies which Certificate Authorities may issue certs for the domain |

## 5. DNS Resolution Process

Steps when a client looks up `www.example.com`:

1. **Check local cache** (browser, OS resolver cache).
2. Query goes to a configured **Recursive Resolver** (e.g., ISP DNS, 8.8.8.8, 1.1.1.1).
3. Resolver queries a **Root server** → returns the TLD server address (`.com`).
4. Resolver queries the **TLD server** → returns the authoritative nameserver for `example.com`.
5. Resolver queries the **Authoritative nameserver** → returns the actual IP (A record).
6. Resolver caches and returns the answer to the client.

This is called a **recursive query** (client asks resolver to do all the work) combined with **iterative queries** (resolver walking the hierarchy itself).

## 6. Types of DNS Servers

| Server Type | Role |
|---|---|
| Recursive Resolver | Does the lookup work on behalf of the client, caches results |
| Root Server | Top of hierarchy, points to TLD servers |
| TLD Server | Points to authoritative servers for domains under that TLD |
| Authoritative Server | Holds the actual DNS records for a specific domain — the "source of truth" |

## 7. Caching & TTL

Each DNS record has a **TTL (Time To Live)** specifying how long resolvers may cache it before re-querying. Short TTLs allow faster propagation of changes (useful during migrations); long TTLs reduce query load and latency.

## 8. DNS Security

DNS is a common attack vector because it's foundational, often unencrypted, and trusted by default.

| Threat | Description |
|---|---|
| DNS Spoofing / Cache Poisoning | Injecting forged responses into a resolver's cache to redirect traffic |
| DNS Tunneling | Exfiltrating data or establishing C2 channels by encoding data in DNS queries |
| DDoS via DNS Amplification | Abusing open resolvers to reflect/amplify traffic at a victim |
| Typosquatting | Registering lookalike domains to trick users |
| DNS Hijacking | Compromising registrar/DNS accounts to redirect a domain |

**Mitigations:**
- **DNSSEC** — cryptographically signs DNS records to prevent spoofing/tampering.
- **DoH (DNS over HTTPS)** / **DoT (DNS over TLS)** — encrypts DNS queries in transit, preventing eavesdropping/manipulation.
- Restricting open resolvers, rate limiting, monitoring for anomalous query patterns (tunneling detection).

## 9. Practical Commands

```bash
# Basic lookup
nslookup example.com
dig example.com

# Query a specific record type
dig example.com MX
dig example.com NS

# Reverse lookup
dig -x 8.8.8.8

# Trace the full resolution path
dig +trace example.com
```

## 10. Interview Questions

1. What port and protocol does DNS primarily use? → **UDP port 53** (TCP 53 for zone transfers/large responses)
2. What record type maps a domain to an IPv4 address? → **A record**
3. What does MX record specify? → **Mail server for the domain**
4. What is the difference between recursive and iterative queries? → **Recursive: resolver does all the work for the client; Iterative: resolver queries each level itself and gets referred onward**
5. What does DNSSEC protect against? → **Spoofing / cache poisoning (via cryptographic signing)**
6. What is DNS tunneling used for? → **Data exfiltration or covert C2 channels via encoded DNS queries**

## 11. Key Points

- DNS translates domain names ↔ IP addresses; runs on port 53 (UDP/TCP).
- Hierarchy: Root → TLD → Authoritative nameserver.
- Key records: **A, AAAA, CNAME, MX, NS, PTR, TXT, SOA**.
- TTL controls caching duration.
- DNSSEC signs records; DoH/DoT encrypt query transport — different problems, both important.
- DNS is a major attack surface: spoofing, tunneling, amplification DDoS.

---
*Related: [[DHCP]], [[Ports]], [[TCP_IP_Model]]*
