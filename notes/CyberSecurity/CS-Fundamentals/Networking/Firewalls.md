## 1. Introduction

A **firewall** is a network security device (hardware, software, or cloud-based) that monitors and controls incoming and outgoing traffic based on a defined set of security rules, acting as a barrier between trusted and untrusted networks (e.g., an internal LAN and the internet). Firewalls are one of the most fundamental perimeter security controls, and modern versions extend far beyond simple IP filtering.

## 2. Why Firewalls Exist

Without filtering, every device on a network is directly reachable from anywhere, drastically expanding the attack surface. Firewalls enforce a **default-deny** posture (block everything not explicitly allowed) or, less securely, a **default-allow** posture (allow everything not explicitly blocked) — default-deny is the security best practice.

## 3. Types of Firewalls (By Generation/Capability)

| Type | Layer | Description |
|---|---|---|
| Packet-Filtering Firewall | Layer 3-4 | Filters based on source/destination IP, port, and protocol; stateless — no awareness of connection context |
| Stateful Inspection Firewall | Layer 3-4 | Tracks the state of active connections (e.g., knows a reply belongs to an established outbound request), allowing more intelligent, context-aware decisions |
| Application Layer (Proxy) Firewall | Layer 7 | Fully inspects application-layer content (e.g., HTTP requests), can enforce protocol-specific rules, acts as an intermediary rather than just passing packets through |
| Next-Generation Firewall (NGFW) | Layer 3-7 | Combines stateful inspection with deep packet inspection, intrusion prevention (IPS), application awareness/control, and often user identity awareness |
| Web Application Firewall (WAF) | Layer 7 | Specifically protects web applications from attacks like SQL injection and XSS by inspecting HTTP/HTTPS traffic content |

## 4. Stateless vs Stateful Filtering

| Aspect | Stateless (Packet Filter) | Stateful |
|---|---|---|
| Awareness | Evaluates each packet independently | Tracks connection state (SYN, ESTABLISHED, etc.) |
| Rule complexity | Must explicitly allow both directions of traffic | Automatically allows return traffic for established connections |
| Performance | Faster, simpler | Slightly more overhead, but far more secure and practical |
| Example | Old-style ACLs on routers | Modern host/network firewalls (iptables, pf, most enterprise firewalls) |

**Why stateful matters:** a stateless firewall allowing outbound HTTP (port 80) must also have a separate rule allowing the *return* traffic on the ephemeral port — a stateful firewall automatically permits return traffic belonging to a connection it already approved, without needing broad additional rules that could be abused.

## 5. Firewall Rule Components

A typical firewall rule (ACL entry) evaluates some combination of:

| Criterion | Example |
|---|---|
| Source IP/Network | 192.168.1.0/24 |
| Destination IP/Network | 10.0.0.5 |
| Source/Destination Port | 443 |
| Protocol | TCP, UDP, ICMP |
| Action | Allow, Deny, Drop, Log |
| Direction | Inbound, Outbound |

**Rule order matters** — most firewalls process rules top-down and stop at the first match, so more specific rules typically need to precede more general ones. An implicit or explicit "deny all" rule is usually placed at the end (default-deny).

## 6. Network-Based vs Host-Based Firewalls

| Type | Location | Example |
|---|---|---|
| Network Firewall | Perimeter, protects an entire network segment | Cisco ASA, Palo Alto, pfSense, cloud Security Groups/NACLs |
| Host-Based Firewall | Runs on an individual device, protects just that host | Windows Defender Firewall, iptables/nftables, macOS pf |

Defense in depth typically uses both — a network firewall at the perimeter plus host firewalls on individual endpoints, so a breach of one layer doesn't leave everything exposed.

## 7. Firewall Placement — DMZ Architecture

A **DMZ (Demilitarized Zone)** is a network segment isolated between two firewalls (or one firewall with multiple interfaces), hosting public-facing services (web servers, mail servers) that need to be reachable from the internet, while keeping the truly internal network separated and protected even if a DMZ host is compromised.

```
Internet → [Firewall] → DMZ (public web server) → [Firewall] → Internal Network
```

## 8. NAT and Firewalls

While NAT (see [[NAT]]) isn't itself a firewall, its side effect of blocking unsolicited inbound connections provides firewall-like behavior for typical home/office networks. Dedicated firewalls provide far more granular, intentional control than this incidental NAT side effect.

## 9. IDS/IPS vs Firewall

| Aspect | Firewall | IDS/IPS |
|---|---|---|
| Primary function | Allows/blocks traffic based on rules | Detects (IDS) or detects+blocks (IPS) malicious patterns/signatures within allowed traffic |
| Placement | At the boundary, controls what gets through | Often inline (IPS) or out-of-band (IDS), inspecting traffic content for threats |
| Relationship | Complementary — a firewall might allow port 443, but an IPS behind it can still catch an attack hidden within that allowed traffic |

Modern NGFWs often integrate IPS functionality directly.

## 10. Common Firewall Evasion/Weaknesses

| Issue | Description |
|---|---|
| Overly permissive rules | "Allow any/any" rules defeat the purpose of having a firewall at all |
| Misconfigured DMZ | Insufficient isolation between DMZ and internal network |
| Application-layer attacks over allowed ports | A firewall allowing port 443 doesn't inspect what's happening inside that encrypted/HTTP traffic unless it's an NGFW/WAF doing deep inspection |
| Tunneling | Attackers tunnel disallowed protocols inside allowed ones (e.g., ICMP tunneling — see [[ICMP]]) |
| Insider threats | Firewalls primarily control north-south (external) traffic — east-west (internal) traffic often has far weaker segmentation |

## 11. Practical Examples

```bash
# Linux iptables — allow inbound SSH, deny everything else inbound
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -j DROP

# Linux nftables/UFW — simpler modern syntax
ufw allow 22/tcp
ufw default deny incoming
```

## 12. Interview Questions

1. What's the difference between a stateless and stateful firewall? → **Stateless evaluates each packet independently; stateful tracks connection state and automatically permits legitimate return traffic**
2. What does "default-deny" mean, and why is it the security best practice? → **Blocking everything not explicitly allowed — minimizes attack surface compared to default-allow**
3. What is a DMZ, and why is it used? → **An isolated network segment for public-facing services, protecting the internal network even if a DMZ host is compromised**
4. What's the difference between an NGFW and a traditional packet-filtering firewall? → **NGFW adds deep packet inspection, application awareness, and often IPS functionality beyond simple IP/port filtering**
5. What's the difference between an IDS and an IPS? → **IDS only detects and alerts; IPS detects and actively blocks malicious traffic inline**
6. Why doesn't a firewall alone protect against application-layer attacks like SQL injection over an allowed HTTPS port? → **A standard firewall doesn't inspect application content — that requires a WAF or application-aware NGFW**

## 13. Key Points

- Firewalls filter traffic based on rules, ideally following a **default-deny** posture.
- **Stateful** inspection (tracking connection context) is standard today over pure stateless packet filtering.
- **NGFWs** add deep packet inspection, application awareness, and often IPS — far beyond basic IP/port filtering.
- **Network firewalls** (perimeter) and **host firewalls** (endpoint) together provide defense in depth.
- A **DMZ** isolates public-facing services from the internal network.
- Firewalls complement, but don't replace, **IDS/IPS** and application-layer protections like **WAFs**.

---
*Related: [[Network_Devices]], [[NAT]], [[ICMP]], [[Proxy_Servers]]*
