## 1. Introduction

A **VLAN (Virtual Local Area Network)** logically segments a single physical switch/network into multiple isolated broadcast domains, as if they were separate physical switches — without needing separate physical hardware. VLANs operate at **Layer 2** and are defined by the **IEEE 802.1Q** standard.

## 2. Why Use VLANs

- **Security** — isolate sensitive traffic (e.g., finance, HR) from general user traffic.
- **Broadcast control** — smaller broadcast domains reduce unnecessary traffic and improve performance.
- **Logical grouping** — group users by department/function regardless of physical location.
- **Simplified management** — moves/adds/changes handled in software rather than re-cabling.
- **Cost savings** — one physical switch can serve multiple logical networks.

## 3. How VLANs Work

Each switch port is assigned to a VLAN (identified by a **VLAN ID**, 1-4094). Devices in the same VLAN can communicate directly at Layer 2; devices in different VLANs **cannot** communicate without a router or Layer 3 switch (**inter-VLAN routing**) — VLANs are separate broadcast domains and, by extension, separate IP subnets by convention.

## 4. VLAN Port Types

| Port Type | Description |
|---|---|
| Access Port | Belongs to exactly one VLAN; used to connect end devices (PCs, printers) — untagged traffic |
| Trunk Port | Carries traffic for multiple VLANs between switches (or to a router); frames are tagged with VLAN ID |

## 5. VLAN Tagging (802.1Q)

Trunk links insert a **4-byte 802.1Q tag** into the Ethernet frame header to identify which VLAN each frame belongs to:

```
[Dest MAC][Src MAC][802.1Q Tag: TPID + VLAN ID][EtherType][Payload][FCS]
```

The tag includes the **VLAN ID (12 bits, supports 4094 VLANs)** and a **priority field (802.1p, for QoS)**.

**Native VLAN:** On a trunk, one VLAN is designated as "native" and its traffic is sent **untagged** — a legacy/compatibility feature that is also a common misconfiguration exploited in VLAN hopping attacks.

## 6. Types of VLANs

| Type | Purpose |
|---|---|
| Default VLAN | VLAN 1 — all ports belong here by default (should be changed/unused in production) |
| Data VLAN | Carries regular user-generated traffic |
| Voice VLAN | Dedicated to VoIP traffic, often prioritized via QoS |
| Management VLAN | Used for switch/router administrative access (SSH, SNMP) |
| Native VLAN | Untagged traffic on a trunk link |
| Black Hole / Unused VLAN | Unused ports assigned here to isolate them until needed |

## 7. Inter-VLAN Routing

Since VLANs are isolated at Layer 2, traffic between them must be routed at Layer 3:

| Method | Description |
|---|---|
| Router-on-a-Stick | A single router interface, subdivided into sub-interfaces (one per VLAN), connects to a trunk port on the switch |
| Layer 3 Switch (SVI) | Switch Virtual Interfaces (one per VLAN) allow the switch itself to route between VLANs at high speed — the modern standard approach |
| Dedicated router per VLAN | Legacy approach, doesn't scale |

## 8. VTP (VLAN Trunking Protocol)

Cisco-proprietary protocol that synchronizes VLAN configuration (VLAN names/IDs, not port assignments) across multiple switches in a domain, avoiding manual configuration on every switch.

| VTP Mode | Behavior |
|---|---|
| Server | Can create/modify/delete VLANs; propagates changes |
| Client | Receives and forwards updates, cannot make changes locally |
| Transparent | Doesn't participate in VTP synchronization, but forwards VTP messages |

**Caution:** A misconfigured VTP server with a higher revision number can accidentally wipe VLAN databases across an entire domain — a well-known real-world outage cause.

## 9. VLAN Security Considerations

| Threat | Description | Mitigation |
|---|---|---|
| VLAN Hopping (Switch Spoofing) | Attacker's device negotiates trunking (DTP) to gain access to all VLANs | Disable DTP/auto-trunk negotiation; explicitly set access/trunk mode |
| VLAN Hopping (Double Tagging) | Attacker sends a frame with two 802.1Q tags; the outer tag is stripped at the first switch, exposing the inner tag to hop into the native VLAN | Avoid using the native VLAN for any real traffic; don't put user ports on VLAN 1 |
| Unused VLAN 1 | Default VLAN can be scanned/attacked if left as-is | Change management VLAN off VLAN 1; disable unused ports and assign to a black-hole VLAN |

## 10. Interview Questions

1. What layer do VLANs primarily operate at? → **Layer 2**
2. What standard defines VLAN tagging? → **IEEE 802.1Q**
3. What's the difference between an access port and a trunk port? → **Access: one VLAN, untagged; Trunk: multiple VLANs, tagged**
4. How many VLANs can 802.1Q support? → **4094 (12-bit VLAN ID)**
5. What is required for devices in different VLANs to communicate? → **A router or Layer 3 switch (inter-VLAN routing)**
6. What VLAN hopping technique exploits the native VLAN? → **Double tagging**

## 11. Key Points

- VLANs create isolated Layer 2 broadcast domains on shared physical switches.
- 802.1Q tags frames on trunk links with a VLAN ID (1-4094).
- Access ports = one VLAN, untagged; Trunk ports = multiple VLANs, tagged.
- Inter-VLAN routing requires a router or Layer 3 switch (SVIs, or router-on-a-stick).
- VLAN hopping (switch spoofing, double tagging) is the primary VLAN-specific attack — mitigate by disabling auto-trunking and avoiding native VLAN use.

---
*Related: [[Switching]], [[Routing]], [[NAT]]*
