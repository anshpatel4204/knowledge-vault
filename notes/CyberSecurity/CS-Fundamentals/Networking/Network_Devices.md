## 1. Introduction

Networks are built from physical and logical devices operating at different OSI layers, each with a specific job in moving, filtering, or translating traffic. Understanding which layer a device operates at explains exactly what it can and can't do — this note gives a consolidated reference across devices covered individually elsewhere in this folder.

## 2. Devices by OSI Layer

| Device | OSI Layer | Function |
|---|---|---|
| Hub | 1 (Physical) | Repeats signal to all ports, no intelligence |
| Repeater | 1 (Physical) | Regenerates/amplifies a signal to extend distance |
| Modem | 1 (Physical) | Converts digital signals to analog (and back) for transmission over telephone/cable lines |
| Switch | 2 (Data Link) | Forwards frames based on MAC address |
| Bridge | 2 (Data Link) | Connects/filters traffic between two network segments based on MAC address (largely superseded by switches) |
| Wireless Access Point (AP) | 1-2 | Bridges wireless clients to a wired network |
| Router | 3 (Network) | Forwards packets between different networks based on IP address |
| Layer 3 Switch | 2-3 | Combines switching speed with inter-VLAN routing capability |
| Firewall | 3-7 (varies) | Filters traffic based on rules — from simple IP/port filtering to full application inspection (see [[Firewalls]]) |
| Load Balancer | 4 or 7 | Distributes traffic across multiple servers |
| Proxy Server | 7 (Application) | Intermediary that forwards/filters application-layer requests (see [[Proxy_Servers]]) |
| Gateway | Any/All | General term for a device translating between two different network systems/protocols |

## 3. Hub (Legacy)

A **Layer 1** device that simply repeats every incoming electrical signal out to all other ports — no intelligence, no addressing awareness. Creates **one shared collision domain** across all connected devices. Essentially obsolete today, fully replaced by switches.

## 4. Switch

A **Layer 2** device forwarding frames intelligently based on the destination **MAC address**, using a learned MAC address table (see [[Switching]] for full detail). Each port is its own collision domain. Modern **Layer 3 switches** add IP-based routing capability, blurring the line with routers.

## 5. Bridge

A simpler, largely historical **Layer 2** device that connects two network segments and filters traffic between them based on MAC address — conceptually a 2-port switch. Modern multi-port switches have effectively replaced bridges in most deployments, though the underlying learning/forwarding logic is the same.

## 6. Router

A **Layer 3** device that forwards packets between different networks/subnets based on **IP address**, using a routing table (see [[Routing]]). Routers connect distinct broadcast domains together — this is exactly why inter-VLAN communication requires a router (or Layer 3 switch).

## 7. Modem

Converts digital data from a computer/network into a signal (analog for traditional phone lines, differently modulated for cable/fiber) suitable for transmission over an ISP's physical medium, and back again. "Modem" = **MO**dulator-**DEM**odulator. Often combined with a router in a single consumer device ("gateway" devices from ISPs).

## 8. Access Point (AP)

Bridges wireless clients (Wi-Fi) to a wired network at Layers 1-2, broadcasting an SSID and handling wireless-specific functions like authentication and encryption (see [[Wireless_Networking]]). A **Wireless Router** combines an AP, switch, and router in one consumer device.

## 9. Gateway (General Concept)

A broader term for any device that translates between two different network environments/protocols — could be a router connecting a LAN to the internet ("default gateway"), a device translating between different protocol stacks entirely (e.g., a VoIP gateway converting between traditional telephony and IP-based voice), or an API gateway at the application layer. Context determines the specific meaning — "default gateway" specifically refers to the router a device sends non-local traffic to.

## 10. Firewall (Brief — see [[Firewalls]] for full detail)

A device or software that filters traffic based on defined security rules, operating anywhere from Layer 3 (packet filtering) up through Layer 7 (application-aware Next-Generation Firewalls).

## 11. Load Balancer

Distributes incoming traffic across multiple backend servers to improve availability, scalability, and fault tolerance. Can operate at **Layer 4** (distributing based on IP/port, faster, protocol-agnostic) or **Layer 7** (distributing based on application content like URL paths or headers, more intelligent but higher overhead).

## 12. Proxy Server (Brief — see [[Proxy_Servers]] for full detail)

An **Application layer** intermediary that sits between clients and servers, forwarding requests on behalf of one side — forward proxies represent clients, reverse proxies represent servers.

## 13. Device Comparison Summary

| Device | Addressing Used | Breaks Up |
|---|---|---|
| Hub | None | Nothing |
| Switch | MAC | Collision domains |
| Router | IP | Broadcast domains |
| Firewall | IP/Port/Application data | Trust zones/security boundaries |

## 14. Interview Questions

1. What's the fundamental difference between a switch and a router in terms of addressing? → **A switch forwards based on MAC address (Layer 2); a router forwards based on IP address (Layer 3)**
2. Why is a hub considered obsolete compared to a switch? → **A hub blindly repeats signals to all ports with no intelligence, creating one large collision domain, while a switch forwards intelligently per-port**
3. What does a Layer 3 switch combine? → **Layer 2 switching speed with Layer 3 inter-VLAN routing capability**
4. What is the difference between a bridge and a switch? → **Functionally similar (Layer 2, MAC-based filtering); a bridge is typically a simple 2-port device, a switch is a multi-port version of the same concept**
5. What does "default gateway" refer to on a home network? → **The router a device sends traffic to when the destination is outside its local subnet**
6. At what layer does a Layer 7 load balancer make decisions, and what can it inspect that a Layer 4 load balancer cannot? → **Application layer — it can inspect content like URL paths, headers, and cookies, not just IP/port**

## 15. Key Points

- Device capability is defined by its **OSI layer**: Hub (L1), Switch (L2), Router (L3), Firewall/Proxy (L3-L7 depending on type).
- **Switches** break up collision domains; **routers** break up broadcast domains.
- **Layer 3 switches** and **wireless routers** are common hybrid devices combining multiple roles.
- "Gateway" is a general/contextual term — most commonly referring to the router forwarding traffic off the local network.
- Load balancers and proxies operate at the application layer for the most intelligent traffic decisions, at the cost of more processing overhead than simple Layer 3/4 devices.

---
*Related: [[Switching]], [[Routing]], [[Firewalls]], [[Proxy_Servers]], [[Wireless_Networking]], [[OSI_MODEL]]*
