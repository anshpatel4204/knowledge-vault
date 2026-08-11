## 1. Introduction

**ARP (Address Resolution Protocol)** resolves a known **IP address** to its corresponding **MAC address** on a local network segment, bridging Layer 3 (IP) addressing with Layer 2 (MAC) addressing so a frame can actually be delivered. Without ARP, a device would know *where* to logically send a packet but not *which physical interface* to hand the frame to. Defined in RFC 826.

## 2. Why ARP Is Needed

IP routing decides which network a packet needs to reach, but actual delivery on a local Ethernet segment happens via **MAC addresses** (see [[MAC_Address]]). When a device wants to send a packet to another IP on the same subnet, it needs to know the destination's MAC address first — ARP is the mechanism that finds it.

## 3. How ARP Works

1. **ARP Request (broadcast):** Device A broadcasts: *"Who has 192.168.1.20? Tell 192.168.1.10 (MAC: AA:AA:AA:AA:AA:AA)."* — sent to the broadcast MAC `FF:FF:FF:FF:FF:FF`, reaching every device on the local segment.
2. **ARP Reply (unicast):** Only the device with `192.168.1.20` responds directly: *"192.168.1.20 is at BB:BB:BB:BB:BB:BB."*
3. **Caching:** Device A stores this IP-to-MAC mapping in its local **ARP table** (ARP cache) so it doesn't need to repeat the request for every packet.

```
A (192.168.1.10) → Broadcast: "Who has 192.168.1.20?"
B (192.168.1.20) → Unicast reply to A: "I have it, my MAC is BB:BB:..."
```

## 4. The ARP Table (ARP Cache)

Each device maintains a table mapping known IPs to MACs, with entries that **age out** after a period of inactivity (forcing a fresh ARP request when needed again) to keep the table current as devices join/leave/change.

```bash
# View ARP table
arp -a          # Windows / macOS / Linux

# Linux alternative
ip neigh show
```

## 5. Gratuitous ARP

An **unsolicited** ARP announcement a device sends without being asked — typically broadcasting its own IP-to-MAC mapping.

**Legitimate uses:**

- Announcing a new IP address when a device boots or changes its address, so other devices update their ARP caches proactively.
- Detecting IP address conflicts (if another device replies claiming the same IP, a conflict exists).
- Failover in High Availability (HA) setups — when a backup device takes over a virtual IP, it sends a gratuitous ARP so switches/routers immediately redirect traffic to its MAC address instead of the failed primary's.

## 6. RARP and Related Protocols (Historical Context)

| Protocol | Purpose |
|---|---|
| ARP | IP → MAC |
| RARP (Reverse ARP) | MAC → IP (legacy, used before DHCP existed, now obsolete) |
| Proxy ARP | A router answers ARP requests on behalf of hosts on a different network segment, making them appear locally reachable |

## 7. ARP and IPv6 — It Doesn't Exist There

IPv6 does **not** use ARP at all — it replaces this function with **NDP (Neighbor Discovery Protocol)**, built on ICMPv6, which handles address resolution, router discovery, and duplicate address detection using multicast instead of broadcast (see [[IPv6]]).

## 8. ARP Security — Spoofing and Poisoning

ARP has **no built-in authentication** — any device on the local segment can claim to own any IP address, and other devices will simply believe the reply and update their cache. This is a fundamental design weakness exploited by:

### ARP Spoofing / ARP Poisoning

An attacker sends forged ARP replies associating **their own MAC address** with another device's IP address (very often the **default gateway's IP**), causing victim devices to send their traffic to the attacker instead — enabling a **Man-in-the-Middle (MITM)** attack where the attacker can intercept, inspect, modify, or drop traffic before optionally forwarding it on.

```
Attacker sends forged reply: "192.168.1.1 (gateway) is at ATTACKER_MAC"
Victim updates its ARP cache, now sends gateway-bound traffic to the attacker
```

### Consequences of ARP Spoofing

- Traffic interception (credential sniffing, session hijacking).
- Denial of Service (attacker simply drops intercepted traffic instead of forwarding it).
- Enables further attacks like DNS spoofing or SSL stripping once traffic flows through the attacker.

## 9. ARP Attack Mitigations

| Mitigation | Description |
|---|---|
| Dynamic ARP Inspection (DAI) | Switch feature that validates ARP packets against a trusted binding table (often built from DHCP Snooping data — see [[DHCP]]), dropping forged replies |
| Static ARP Entries | Manually configuring critical IP-MAC mappings (e.g., for the gateway) so they can't be overwritten by a spoofed reply — doesn't scale well but useful for critical infrastructure |
| Port Security | Limiting which MAC addresses are allowed on a switch port, reducing an attacker's ability to inject traffic under a spoofed identity |
| ARP Monitoring Tools | Tools like `arpwatch` detect and alert on unexpected IP-to-MAC mapping changes |
| Network Segmentation | Smaller broadcast domains (VLANs) limit the scope an attacker on one segment can reach via ARP spoofing |

## 10. Practical Commands

```bash
# View ARP table
arp -a

# Add a static ARP entry (Linux)
sudo arp -s 192.168.1.1 AA:AA:AA:AA:AA:AA

# Clear the ARP cache (Windows)
netsh interface ip delete arpcache
```

## 11. Interview Questions

1. What does ARP resolve? → **An IP address to its corresponding MAC address on the local network segment**
2. Is an ARP request sent as a broadcast or unicast? → **Broadcast (the reply is unicast)**
3. What is gratuitous ARP, and give one legitimate use. → **An unsolicited ARP announcement; used for IP conflict detection or HA failover to redirect traffic to a new MAC**
4. Why is ARP inherently insecure? → **It has no authentication — any device can claim to own any IP address, and replies are trusted by default**
5. What attack exploits ARP's lack of authentication to become a man-in-the-middle? → **ARP spoofing/poisoning**
6. What switch feature mitigates ARP spoofing by validating ARP packets against a trusted binding table? → **Dynamic ARP Inspection (DAI)**
7. What replaces ARP in IPv6? → **NDP (Neighbor Discovery Protocol), built on ICMPv6**

## 12. Key Points

- ARP resolves **IP → MAC** on the local network segment via broadcast request / unicast reply.
- Results are cached in the **ARP table**, which entries age out of over time.
- **Gratuitous ARP** announces a mapping unsolicited — used for conflict detection and HA failover.
- ARP has **no authentication**, making **ARP spoofing/poisoning** a classic and effective MITM technique, usually targeting the default gateway.
- Mitigated with **Dynamic ARP Inspection**, static entries for critical devices, and port security.
- **IPv6 doesn't use ARP** — it uses NDP instead.

---
*Related: [[IP_Address]], [[MAC_Address]], [[Switching]], [[DHCP]]*
