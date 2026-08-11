## 1. Introduction

**Switching** is the process of forwarding frames between devices on the **same network segment**, performed by **switches** operating primarily at **Layer 2 (Data Link)** of the OSI model. Switches use **MAC addresses** to make forwarding decisions, unlike routers which use IP addresses at Layer 3.

## 2. Switch vs Hub vs Router

| Device | Layer | Forwarding Basis | Behavior |
|---|---|---|---|
| Hub | Layer 1 | None | Broadcasts every incoming signal out all ports (creates one collision domain) |
| Switch | Layer 2 | MAC address | Forwards intelligently only to the port with the destination MAC |
| Router | Layer 3 | IP address | Forwards between different networks/subnets |

## 3. The MAC Address Table (CAM Table)

A switch builds and maintains a **MAC address table** (Content Addressable Memory table) that maps MAC addresses to the physical ports they were learned on.

**Switch operation — Learn, Flood, Forward:**

1. **Learn** — When a frame arrives, the switch records the source MAC address and incoming port in its table.
2. **Flood** — If the destination MAC isn't in the table (or it's a broadcast), the switch floods the frame out all ports except the one it arrived on.
3. **Forward** — If the destination MAC is known, the switch forwards the frame only out that specific port.
4. **Filter** — If source and destination are on the same port, the frame is dropped (no need to forward).

Entries in the MAC table **age out** after a period of inactivity (default often 300 seconds) to keep the table current.

## 4. Collision Domains vs Broadcast Domains

| Concept | Definition | Affected By |
|---|---|---|
| Collision Domain | A network segment where frame collisions can occur | Each switch port is its own collision domain (hubs share one big domain) |
| Broadcast Domain | A network segment where broadcasts are heard by all members | Bounded by routers or VLANs — a switch, by default, is one large broadcast domain |

Switches break up collision domains (one per port); routers (and VLANs) break up broadcast domains.

## 5. Switching Methods

| Method | Description | Latency | Error Checking |
|---|---|---|---|
| Store-and-Forward | Receives the entire frame, checks CRC, then forwards | Highest | Yes — drops corrupt frames |
| Cut-Through | Forwards as soon as the destination MAC is read (start of frame) | Lowest | No — corrupt frames may be forwarded |
| Fragment-Free | Waits for the first 64 bytes (past the collision window) before forwarding | Medium | Partial |

## 6. Spanning Tree Protocol (STP)

Redundant links between switches improve reliability but create **Layer 2 loops**, causing **broadcast storms** and MAC table instability. **STP (IEEE 802.1D)** prevents loops by logically blocking redundant paths while keeping them available as backups.

**STP port roles:**

| Role | Description |
|---|---|
| Root Port | Best path back to the root bridge (one per non-root switch) |
| Designated Port | Best path for a given segment (forwards traffic) |
| Blocking Port | Redundant path, blocked to prevent a loop, activates if the active path fails |

**STP port states:** Blocking → Listening → Learning → Forwarding (Disabled if administratively shut down).

**Faster variants:** **RSTP (802.1w)** — Rapid STP, converges in seconds instead of ~30-50s. **MSTP (802.1s)** — Multiple STP instances mapped to different VLANs for load balancing.

## 7. Switch Security Threats & Mitigations

| Threat | Description | Mitigation |
|---|---|---|
| MAC Flooding | Attacker floods the switch with fake source MACs to overflow the CAM table, forcing it to flood all traffic like a hub (enabling sniffing) | Port Security (limit MACs per port) |
| ARP Spoofing / Poisoning | Attacker sends forged ARP replies to redirect traffic through themselves (MITM) | Dynamic ARP Inspection (DAI) |
| Rogue DHCP | Unauthorized DHCP server hands out malicious configs | DHCP Snooping (see [[DHCP]]) |
| VLAN Hopping | Attacker exploits trunk misconfiguration to access other VLANs | Disable auto-trunking, explicitly configure trunk ports (see [[VLAN]]) |
| Spanning Tree Attacks | Attacker injects BPDUs to become root bridge and intercept traffic | BPDU Guard, Root Guard |

## 8. Port Security (Example Concept)

Restricts which/how many MAC addresses can connect to a switch port:

```
switchport port-security
switchport port-security maximum 2
switchport port-security violation shutdown
```

Violation actions: **Protect** (drop offending traffic silently), **Restrict** (drop + log), **Shutdown** (disable the port, default and most secure).

## 9. Layer 3 Switches

A **Layer 3 switch** combines traditional Layer 2 switching with routing capability, allowing it to route between VLANs at near wire-speed without needing a separate router — common in modern enterprise core/distribution layers.

## 10. Interview Questions

1. At which OSI layer does a switch primarily operate? → **Layer 2 (Data Link)**
2. What table does a switch use to make forwarding decisions? → **MAC address table (CAM table)**
3. What does each switch port represent in terms of collision domains? → **Its own collision domain**
4. What protocol prevents Layer 2 loops? → **STP (Spanning Tree Protocol)**
5. What attack overflows the CAM table to force flooding? → **MAC flooding**
6. What feature limits the number of MAC addresses on a port? → **Port Security**

## 11. Key Points

- Switches operate at Layer 2, forwarding based on MAC addresses via the CAM table.
- Learn → Flood → Forward → Filter is the core switching logic.
- Each switch port = one collision domain; a switch is one broadcast domain unless VLANs are used.
- STP/RSTP prevent Layer 2 loops from redundant links.
- Key attacks: MAC flooding, ARP spoofing, VLAN hopping — mitigated with Port Security, DAI, and proper trunk config.

---
*Related: [[VLAN]], [[Routing]], [[OSI_MODEL]]*
