## 1. Introduction

**IPv4 (Internet Protocol version 4)** is the fourth version of the Internet Protocol and the primary addressing system that has run the internet since the 1980s. It uses **32-bit addresses**, providing roughly **4.3 billion** unique addresses — a number long since exhausted by global demand, which is the main driver behind IPv6 adoption.

## 2. Address Format

An IPv4 address is 32 bits, written as **four decimal octets separated by dots** (dotted-decimal notation), e.g. `192.168.1.10`.

Each octet ranges from **0-255** (8 bits = 2^8 values).

```
11000000.10101000.00000001.00001010
   192   .   168  .    1   .   10
```

## 3. Address Classes (Classful Addressing — Legacy)

| Class | Leading Bits | Range | Default Mask | Use |
|---|---|---|---|---|
| A | 0 | 1.0.0.0 - 126.255.255.255 | /8 (255.0.0.0) | Large networks |
| B | 10 | 128.0.0.0 - 191.255.255.255 | /16 (255.255.0.0) | Medium networks |
| C | 110 | 192.0.0.0 - 223.255.255.255 | /24 (255.255.255.0) | Small networks |
| D | 1110 | 224.0.0.0 - 239.255.255.255 | N/A | Multicast |
| E | 1111 | 240.0.0.0 - 255.255.255.255 | N/A | Experimental/Reserved |

Note: **127.0.0.0/8** is reserved for **loopback** (127.0.0.1 = localhost). Classful addressing has been replaced by CIDR in practice, but the ranges are still useful reference points.

## 4. Private (RFC 1918) vs Public Addresses

| Class | Private Range |
|---|---|
| A | 10.0.0.0 - 10.255.255.255 |
| B | 172.16.0.0 - 172.31.255.255 |
| C | 192.168.0.0 - 192.168.255.255 |

Private addresses are not routable on the public internet — devices using them reach the internet via **NAT** (see [[NAT]]). Public addresses are globally unique and assigned by regional registries (ARIN, RIPE, APNIC, etc.).

## 5. Special/Reserved Addresses

| Address | Purpose |
|---|---|
| 0.0.0.0 | "This network" / unspecified address |
| 127.0.0.1 | Loopback |
| 169.254.0.0/16 | APIPA (Automatic Private IP Addressing) — self-assigned when DHCP fails |
| 255.255.255.255 | Limited broadcast |
| 224.0.0.0/4 | Multicast |

## 6. Subnet Mask & CIDR Notation

A **subnet mask** separates the **network portion** from the **host portion** of an address. **CIDR (Classless Inter-Domain Routing)** notation expresses this as a suffix, e.g. `/24` = 24 network bits.

| CIDR | Subnet Mask | Hosts (usable) |
|---|---|---|
| /8 | 255.0.0.0 | 16,777,214 |
| /16 | 255.255.0.0 | 65,534 |
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /30 | 255.255.255.252 | 2 |

Usable hosts = 2^(host bits) - 2 (subtracting the network address and broadcast address).

## 7. Subnetting Example

Given `192.168.10.0/26`:

- Subnet mask: `255.255.255.192`
- Block size: 256 - 192 = 64
- Subnets: `192.168.10.0`, `.64`, `.128`, `.192`
- For `192.168.10.64/26`:
  - Network address: `192.168.10.64`
  - Usable range: `192.168.10.65` - `192.168.10.126`
  - Broadcast address: `192.168.10.127`

## 8. Network Address, Broadcast Address, Host Address

| Term | Description |
|---|---|
| Network Address | First address in a subnet — identifies the network itself (all host bits = 0) |
| Broadcast Address | Last address in a subnet — sends to all hosts on that subnet (all host bits = 1) |
| Host Address | Any address in between — assignable to a device |

## 9. Static vs Dynamic Addressing

- **Static** — manually configured, doesn't change; used for servers, printers, routers.
- **Dynamic** — assigned automatically via **DHCP** (see [[DHCP]]), typical for end-user devices.

## 10. IPv4 Header (Brief)

Key fields relevant to security/troubleshooting: **TTL** (Time to Live — decremented per hop, prevents infinite loops, used by `traceroute`), **Protocol** (indicates TCP=6, UDP=17, ICMP=1), **Source/Destination IP**, **Fragmentation flags**.

## 11. IPv4 Exhaustion

With only ~4.3 billion addresses and billions of connected devices, IPv4 space ran out at the registry level around 2011-2019 (region-dependent). Mitigations: **NAT**, **CIDR** (replacing wasteful classful allocation), and ultimately migration to **IPv6**.

## 12. Interview Questions

1. How many bits is an IPv4 address? → **32 bits**
2. What is the loopback address? → **127.0.0.1**
3. What CIDR notation represents 255.255.255.0? → **/24**
4. Name the three private IP ranges. → **10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16**
5. How do you calculate usable hosts in a subnet? → **2^(host bits) - 2**
6. What does APIPA provide when DHCP fails? → **A 169.254.x.x address**

## 13. Key Points

- IPv4 = 32-bit address, ~4.3 billion total addresses.
- Written in dotted-decimal (e.g., 192.168.1.1).
- Private ranges: 10.x, 172.16-31.x, 192.168.x — require NAT to reach the internet.
- CIDR replaced classful addressing for efficient allocation.
- Subnet mask defines network vs host bits; usable hosts = 2^h - 2.

---
*Related: [[IPv6]], [[NAT]], [[DHCP]], [[TCP_IP_Model]]*
