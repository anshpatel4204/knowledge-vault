## 1. Introduction

A **network topology** describes the physical or logical arrangement of devices and connections in a network. Choice of topology affects cost, fault tolerance, scalability, and troubleshooting complexity. This note also covers **network types by geographic scale** (LAN/WAN/MAN/PAN), a closely related fundamental concept.

## 2. Physical vs Logical Topology

| Type | Describes |
|---|---|
| Physical Topology | How devices are actually cabled/connected in the real world |
| Logical Topology | How data actually flows between devices, regardless of physical layout (e.g., modern switched Ethernet is physically a star but logically can behave differently at Layer 2) |

## 3. Common Topologies

### Bus Topology

All devices connect to a single shared central cable (the "bus").

- **Pros:** cheap, simple, minimal cabling.
- **Cons:** a single cable break disrupts the entire network; performance degrades as devices are added (shared medium); difficult to troubleshoot.
- **Status:** obsolete for modern networks (legacy coax Ethernet, 10BASE2/5).

### Star Topology

All devices connect individually to a central device (hub or switch).

- **Pros:** a single cable failure only affects one device; easy to add/remove devices; centralized management/troubleshooting.
- **Cons:** the central device is a single point of failure for the whole network.
- **Status:** the dominant topology in modern networks (every device connects to a switch).

### Ring Topology

Each device connects to exactly two others, forming a closed loop; data travels around the ring (often in one direction).

- **Pros:** predictable performance, no collisions in token-based implementations (e.g., Token Ring, FDDI).
- **Cons:** a single break can disrupt the entire ring (mitigated by dual-ring designs); adding/removing devices can disrupt traffic.
- **Status:** largely legacy (Token Ring, FDDI), though the conceptual pattern persists in some carrier/metro network designs.

### Mesh Topology

Every device connects to every other device (**full mesh**), or to several others (**partial mesh**).

- **Pros:** highly redundant and fault-tolerant — multiple paths exist between any two nodes.
- **Cons:** expensive and complex to cable/maintain at scale (`n(n-1)/2` links for full mesh); rarely used at the LAN device level.
- **Status:** used at the WAN/core network level (e.g., ISP backbones, redundant routing between data centers) and in wireless mesh Wi-Fi systems for home coverage.

### Hybrid Topology

A combination of two or more topologies — most real-world enterprise networks are hybrids (e.g., a star topology at the access layer, connected via a partial mesh at the core/distribution layer for redundancy).

### Point-to-Point

The simplest topology — a direct link between exactly two devices (e.g., a WAN link between two office sites, or a direct crossover cable connection).

## 4. Topology Comparison Table

| Topology | Fault Tolerance | Cost | Scalability | Common Today? |
|---|---|---|---|---|
| Bus | Poor | Low | Poor | No (obsolete) |
| Star | Moderate (central device is SPOF) | Moderate | Good | Yes — dominant |
| Ring | Poor-Moderate | Moderate | Moderate | Rare (legacy) |
| Mesh | Excellent | High | Poor at scale | WAN/core/backbone only |
| Hybrid | Good (design-dependent) | Varies | Good | Yes — most enterprise networks |

## 5. Network Types by Geographic Scale

| Type | Full Name | Scale | Example |
|---|---|---|---|
| PAN | Personal Area Network | A few meters | Bluetooth devices, a phone connected to a smartwatch |
| LAN | Local Area Network | A single building/campus | Home Wi-Fi, office network |
| CAN | Campus Area Network | Multiple buildings, one organization/location | University campus network |
| MAN | Metropolitan Area Network | A city | A city government's interconnected buildings, cable ISP infrastructure |
| WAN | Wide Area Network | Regions/countries/globally | The internet itself; a company connecting offices across countries via MPLS/VPN |
| SAN | Storage Area Network | Data center scale | Dedicated high-speed network connecting servers to shared storage |

**LAN vs WAN — key practical distinction:** LANs are typically owned/controlled entirely by one organization and use high-speed, low-latency links (Ethernet); WANs typically span links owned by third parties (ISPs, telecom carriers) and have comparatively higher latency and lower bandwidth per cost.

## 6. Topologies in Modern Practice — The Hierarchical/Three-Tier Model

Modern enterprise networks rarely use one pure topology; the common **three-tier hierarchical design** is a structured hybrid:

```
Core Layer       →  High-speed backbone, often partial-mesh for redundancy
Distribution Layer →  Aggregates access layer traffic, applies policy/routing
Access Layer      →  Star topology connecting end-user devices to switches
```

This design balances the star topology's simplicity/manageability at the edge with mesh-like redundancy where it matters most (the core), avoiding mesh's cabling explosion at large scale.

## 7. Interview Questions

1. What's the main disadvantage of a star topology? → **The central device (switch/hub) is a single point of failure**
2. Why is full mesh topology rarely used at the LAN access layer? → **Cabling/link requirements grow as n(n-1)/2, becoming impractical at scale**
3. What topology do most modern LANs physically use? → **Star (all devices connect to a central switch)**
4. What's the difference between a LAN and a WAN? → **LAN is a small, single-location network typically owned by one organization; WAN spans larger geography, often over third-party carrier links**
5. What network type describes a Bluetooth connection between a phone and headphones? → **PAN (Personal Area Network)**
6. What topology does the modern three-tier hierarchical network design combine? → **Star at the access layer with mesh-like redundancy at the core**

## 8. Key Points

- Topologies: **Bus** (obsolete), **Star** (dominant today), **Ring** (legacy), **Mesh** (redundant but costly, used at core/WAN level), **Hybrid** (most real networks).
- Network scale types: **PAN < LAN < CAN < MAN < WAN**, plus the specialized **SAN**.
- Modern enterprise design uses a **three-tier hierarchical model** — star at the access layer, mesh-like redundancy at the core.
- Physical topology (cabling) and logical topology (data flow) can differ.

---
*Related: [[Network_Devices]], [[Switching]], [[Routing]], [[OSI_MODEL]]*
