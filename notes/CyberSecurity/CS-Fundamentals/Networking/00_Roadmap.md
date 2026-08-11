## How to Use This Folder

This roadmap gives the recommended reading order for the Networking folder — from absolute fundamentals through addressing, core protocols, switching/routing, and finally perimeter/wireless security. Each note links forward to related topics via `[[wikilinks]]`, but reading in this order builds context progressively so nothing assumes knowledge you haven't reached yet.

**Start here:** [[CIA_Triad]]
**Finish here:** [[Wireless_Networking]]

## Stage 1 — Security & Model Fundamentals

Establishes the vocabulary and mental models everything else builds on.

1. [[CIA_Triad]] — the goals all networking/security controls serve
2. [[AAA]] — how access is authenticated, authorized, and logged
3. [[OSI_MODEL]] — the 7-layer reference model used to reason about everything that follows
4. [[TCP_IP_Model]] — the 4-layer model actually implemented on the real internet

## Stage 2 — Topology & Devices

The physical/logical shape of a network and what hardware builds it.

5. [[Network_Topologies]] — bus/star/ring/mesh, and LAN/WAN/MAN/PAN scale
6. [[Network_Devices]] — hub, switch, router, gateway, load balancer at a glance

## Stage 3 — Addressing

How devices are identified, locally and globally.

7. [[IP_Address]] — the general addressing concept
8. [[IPv4]] — structure, classes, private/public ranges
9. [[Subnet_Mask]] — network vs host bits, CIDR, VLSM
10. [[IPv6]] — the modern addressing standard
11. [[MAC_Address]] — hardware addressing at Layer 2
12. [[ARP]] — bridging IP addresses to MAC addresses

## Stage 4 — Transport & Core Protocols

How data actually moves once addressing is understood.

13. [[Ports]] — how one IP serves many services
14. [[TCP_Protocol]] — reliable, connection-oriented transport
15. [[UDP_Protocol]] — fast, connectionless transport
16. [[ICMP]] — diagnostics, ping, traceroute
17. [[DNS]] — name resolution
18. [[DHCP]] — automatic address assignment

## Stage 5 — Switching & Routing

How traffic is forwarded within and between networks.

19. [[Switching]] — Layer 2 forwarding, MAC tables, STP
20. [[VLAN]] — logical segmentation of a switched network
21. [[Routing]] — Layer 3 forwarding between networks
22. [[NAT]] — translating private addresses for internet access

## Stage 6 — Perimeter, Proxies & Remote Access

Where security controls and traffic intermediaries sit.

23. [[Firewalls]] — traffic filtering and access control
24. [[Proxy_Servers]] — forward/reverse proxies, load balancing
25. [[VPN]] — encrypted tunnels across untrusted networks

## Stage 7 — Wireless (Capstone)

Combines addressing, protocols, and security concepts from every prior stage into the wireless-specific context.

26. [[Wireless_Networking]] — Wi-Fi standards, WPA2/WPA3, wireless attacks

## Quick Reference — Full Order

CIA_Triad → AAA → OSI_MODEL → TCP_IP_Model → Network_Topologies → Network_Devices → IP_Address → IPv4 → Subnet_Mask → IPv6 → MAC_Address → ARP → Ports → TCP_Protocol → UDP_Protocol → ICMP → DNS → DHCP → Switching → VLAN → Routing → NAT → Firewalls → Proxy_Servers → VPN → Wireless_Networking
