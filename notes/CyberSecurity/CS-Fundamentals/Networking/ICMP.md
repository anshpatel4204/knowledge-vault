## 1. Introduction

**ICMP (Internet Control Message Protocol)** is a Network layer protocol used for **diagnostics, error reporting, and control messages** between network devices — it doesn't carry application data like TCP/UDP do. It's the protocol behind everyday tools like `ping` and `traceroute`, and it's how routers tell a sender "I couldn't deliver your packet." Defined in RFC 792 (ICMPv4); IPv6 uses **ICMPv6** (RFC 4443), which additionally absorbs ARP's role via NDP (see [[ARP]]).

## 2. ICMP Is Not TCP or UDP

ICMP operates directly over IP (IP protocol number **1**) — it has no port numbers and doesn't use TCP or UDP at all. This is a common point of confusion: ICMP is its own Layer 3 protocol, at the same conceptual level as IP itself, not a Transport layer protocol.

## 3. ICMP Message Structure

Every ICMP message includes a **Type** and **Code** field identifying what kind of message it is:

| Type | Name | Purpose |
|---|---|---|
| 0 | Echo Reply | Response to a ping request |
| 3 | Destination Unreachable | Packet couldn't be delivered (various codes: network unreachable, host unreachable, port unreachable, etc.) |
| 5 | Redirect | Router informs a host of a better route for future packets |
| 8 | Echo Request | Used by `ping` to test reachability |
| 11 | Time Exceeded | TTL reached 0 before the packet reached its destination (used by `traceroute`) |

## 4. Ping — Echo Request/Reply

`ping` sends an **ICMP Echo Request (Type 8)** to a target; if reachable, the target responds with an **ICMP Echo Reply (Type 0)**. Used to test basic connectivity and measure round-trip latency.

```bash
ping 8.8.8.8

Reply from 8.8.8.8: bytes=32 time=14ms TTL=117
```

## 5. Traceroute — Using TTL and Time Exceeded

`traceroute` (Linux/macOS) / `tracert` (Windows) maps the path packets take to a destination by exploiting **TTL (Time to Live)**:

1. Sends a packet with **TTL=1**. The first router decrements TTL to 0, discards the packet, and replies with an **ICMP Time Exceeded (Type 11)** message — revealing hop 1's identity.
2. Sends a packet with **TTL=2** — the second router along the path replies with Time Exceeded, revealing hop 2.
3. This repeats, incrementing TTL by 1 each round, until the destination is finally reached (replying with Echo Reply or Port Unreachable, depending on OS).

```bash
traceroute google.com     # Linux/macOS (uses UDP by default, ICMP with -I)
tracert google.com        # Windows (uses ICMP by default)
```

## 6. Destination Unreachable — Common Codes

| Code | Meaning |
|---|---|
| 0 | Network Unreachable |
| 1 | Host Unreachable |
| 2 | Protocol Unreachable |
| 3 | Port Unreachable (commonly seen when probing a closed UDP port) |
| 13 | Communication Administratively Prohibited (often indicates a firewall block) |

## 7. Path MTU Discovery

ICMP Type 3, Code 4 ("Fragmentation Needed and DF bit set") is used in **Path MTU Discovery** — when a router needs to fragment a packet marked "Don't Fragment," it instead drops it and sends this ICMP message back to the sender, who then reduces its packet size for that path. **Blocking all ICMP at a firewall can silently break this mechanism**, causing mysterious connectivity issues for certain traffic (a classic, hard-to-diagnose real-world networking problem).

## 8. ICMP Redirect

Routers send **ICMP Redirect (Type 5)** to inform a host of a more direct route to a destination than the one it's currently using — normally a legitimate optimization, but attackers can abuse forged redirects to manipulate routing on a victim (an ICMP-based MITM vector), which is why many networks disable accepting ICMP redirects on hosts/routers by default in security-conscious configurations.

## 9. ICMP Security Considerations

| Concern | Description |
|---|---|
| Ping Sweeps | Attackers send ICMP Echo Requests across an IP range for host discovery/reconnaissance |
| Ping of Death | Historic attack sending oversized/malformed ICMP packets to crash older systems — largely patched today |
| ICMP Flood (Smurf Attack) | Attacker spoofs a victim's IP and broadcasts ICMP Echo Requests to a whole subnet, causing all hosts to reply directly to the victim, overwhelming it — mitigated today by disabling directed broadcast forwarding on routers |
| ICMP Tunneling | Encoding data (e.g., C2 traffic or exfiltrated data) inside ICMP Echo packets to evade firewalls that allow ping but don't inspect payload content |
| ICMP Redirect Abuse | Forged redirects manipulating a victim's routing table |

**Mitigations:** rate-limiting ICMP, blocking ICMP redirects, disabling directed broadcasts, inspecting/limiting ICMP payload size at the firewall — while being careful not to block ICMP entirely, since that breaks legitimate diagnostics and Path MTU Discovery.

## 10. Should You Block ICMP Entirely? (Common Misconception)

Completely blocking ICMP at a firewall is a common but often counterproductive security practice — it breaks `ping`/`traceroute` diagnostics AND Path MTU Discovery (causing silent packet loss for certain traffic), while providing only weak protection (attackers have many other reconnaissance methods). Best practice is typically to **rate-limit and selectively filter** ICMP rather than block it outright.

## 11. Interview Questions

1. What Transport layer protocol does ICMP use — TCP or UDP? → **Neither — ICMP runs directly over IP (protocol number 1)**
2. What ICMP message types does `ping` use? → **Type 8 (Echo Request) and Type 0 (Echo Reply)**
3. How does `traceroute` use TTL and ICMP to map a network path? → **It sends packets with incrementing TTL values, relying on routers' ICMP Time Exceeded replies to reveal each hop**
4. What can happen if a firewall blocks all ICMP traffic? → **It can silently break Path MTU Discovery, causing mysterious connectivity/performance issues for certain traffic**
5. What is a Smurf attack? → **A spoofed ICMP broadcast flood that causes an entire subnet to reply to a victim, overwhelming them**
6. What is ICMP tunneling used for maliciously? → **Hiding C2 traffic or exfiltrated data inside ICMP packets to bypass firewalls that permit ping but don't inspect payloads**

## 12. Key Points

- ICMP is a **Layer 3 diagnostic/control protocol** — not TCP or UDP, no ports.
- Powers `ping` (Echo Request/Reply) and `traceroute` (TTL + Time Exceeded).
- Critical for **Path MTU Discovery** — blocking ICMP entirely can silently break legitimate traffic.
- Security concerns: **ping sweeps, Smurf attacks, ICMP tunneling, redirect abuse** — mitigated via rate-limiting and selective filtering, not blanket blocking.
- IPv6 uses **ICMPv6**, which also handles Neighbor Discovery (replacing ARP).

---
*Related: [[IP_Address]], [[ARP]], [[Routing]], [[Firewalls]]*
