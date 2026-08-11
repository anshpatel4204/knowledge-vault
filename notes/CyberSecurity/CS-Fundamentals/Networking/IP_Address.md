## 1. Introduction

An **IP Address (Internet Protocol Address)** is a unique numerical identifier assigned to every device on a network, allowing it to send and receive data at **Layer 3 (Network)** of the OSI model. It works like a postal address for a device — every packet needs a source and destination IP to know where it came from and where it's going. There are two versions in use today: **IPv4** and **IPv6** (see [[IPv4]] and [[IPv6]] for protocol-specific depth — this note covers the concept of IP addressing broadly).

## 2. IPv4 vs IPv6 (Quick Recap)

| Aspect | IPv4 | IPv6 |
|---|---|---|
| Length | 32 bits | 128 bits |
| Format | Dotted decimal (192.168.1.1) | Hex, colon-separated (2001:db8::1) |
| Total addresses | ~4.3 billion | ~340 undecillion |
| Written example | 10.0.0.5 | fe80::1a2b:3c4d |

## 3. Public vs Private IP Addresses

| Type | Description | Example |
|---|---|---|
| Public IP | Globally unique, routable on the internet, assigned by ISPs/registries | 8.8.8.8 |
| Private IP | Used within local networks only, not routable on the internet, requires NAT to reach the internet | 192.168.1.10 |

**Private IPv4 ranges (RFC 1918):**

| Class | Range |
|---|---|
| A | 10.0.0.0 - 10.255.255.255 |
| B | 172.16.0.0 - 172.31.255.255 |
| C | 192.168.0.0 - 192.168.255.255 |

## 4. Static vs Dynamic IP Addresses

| Type | Description | Typical Use |
|---|---|---|
| Static | Manually assigned, never changes | Servers, printers, routers, network infrastructure |
| Dynamic | Automatically assigned by DHCP, can change over time | End-user devices — laptops, phones |

See [[DHCP]] for how dynamic assignment actually works.

## 5. Logical vs Physical Addressing

IP addresses are **logical** addresses — assigned by software/configuration and can change. This contrasts with **MAC addresses**, which are **physical** addresses burned into network hardware (see [[MAC_Address]]). A device typically has one MAC address per network interface, but its IP address can change depending on which network it joins.

| Aspect | IP Address (Logical) | MAC Address (Physical) |
|---|---|---|
| Layer | Network (L3) | Data Link (L2) |
| Assigned by | Software/DHCP/admin | Manufacturer (hardware) |
| Can change | Yes | Rarely (can be spoofed/changed in software) |
| Scope | Routable across networks | Only relevant on the local segment |

## 6. How IP Addresses Are Used in Communication

1. A device is assigned an IP address (statically or via DHCP).
2. When sending data, the source IP and destination IP are placed in the IP header.
3. Routers use the destination IP to forward the packet toward the correct network (see [[Routing]]).
4. Within the local segment, **ARP (Address Resolution Protocol)** maps the destination IP to the corresponding MAC address so the frame can actually be delivered at Layer 2.

## 7. Special IP Addresses

| Address | Meaning |
|---|---|
| 127.0.0.1 | Loopback (IPv4) |
| ::1 | Loopback (IPv6) |
| 0.0.0.0 | Unspecified / "this network" |
| 255.255.255.255 | Limited broadcast (IPv4) |
| 169.254.x.x | APIPA — self-assigned when DHCP fails |
| 224.0.0.0/4 | Multicast (IPv4) |

## 8. Subnetting and the Network/Host Split

Every IP address is logically divided into a **network portion** and a **host portion**, defined by a **subnet mask** or CIDR prefix (e.g., `/24`). This determines which addresses belong to the same local network and which require routing to reach. See [[Subnet_Mask]] for the full breakdown and calculations.

## 9. How to View Your IP Address

```bash
# Windows
ipconfig

# Linux / macOS
ip addr
ifconfig

# Public IP (as seen by the internet)
curl ifconfig.me
```

## 10. IP Address Security Considerations

- **IP Spoofing** — forging the source IP in a packet to impersonate another host, used in DDoS reflection attacks and to bypass IP-based access controls.
- **Geolocation exposure** — public IPs can reveal approximate physical location/ISP, often masked using VPNs or proxies (see [[VPN]]).
- **IP-based access control** — firewalls/ACLs frequently allow-list or block traffic by IP; spoofing and NAT can both complicate this trust model.
- **Private IP leakage** — misconfigurations can expose internal private IPs in HTTP headers or error messages, aiding reconnaissance.

## 11. Interview Questions

1. What layer does IP addressing operate at? → **Layer 3 (Network)**
2. What's the difference between a public and private IP? → **Public: globally routable; Private: only valid within a local network**
3. What's the difference between a static and dynamic IP? → **Static: manually fixed; Dynamic: automatically assigned, can change (via DHCP)**
4. What protocol maps an IP address to a MAC address on the local segment? → **ARP**
5. What is IP spoofing? → **Forging the source IP address in a packet to impersonate another host**
6. Is an IP address a logical or physical address? → **Logical**

## 12. Key Points

- IP address = unique Layer 3 identifier for a device, in IPv4 (32-bit) or IPv6 (128-bit) form.
- Public IPs are globally routable; private IPs require NAT to reach the internet.
- Static IPs are fixed; dynamic IPs are assigned via DHCP and can change.
- IP addresses are logical (software-assigned); MAC addresses are physical (hardware-assigned).
- ARP bridges the two — resolving IP addresses to MAC addresses for local delivery.

---
*Related: [[IPv4]], [[IPv6]], [[Subnet_Mask]], [[MAC_Address]], [[DHCP]]*
