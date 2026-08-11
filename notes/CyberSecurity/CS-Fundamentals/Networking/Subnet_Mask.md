## 1. Introduction

A **Subnet Mask** is a 32-bit value (for IPv4) that divides an IP address into two parts: the **network portion** and the **host portion**. It tells devices which part of an address identifies "which network" and which part identifies "which specific device on that network." Without a subnet mask, a device has no way to know if another IP is on its local segment or requires routing to reach.

## 2. How a Subnet Mask Works

A subnet mask is written in the same dotted-decimal format as an IP address, but it works as a **bitmask**:

- A binary **1** in the mask = that bit is part of the **network** portion.
- A binary **0** in the mask = that bit is part of the **host** portion.

```
IP Address:   192.168.1.10   = 11000000.10101000.00000001.00001010
Subnet Mask:  255.255.255.0  = 11111111.11111111.11111111.00000000
                                --------Network--------|---Host---
```

Here, `192.168.1` is the network, and `.10` identifies the specific host within it.

## 3. CIDR Notation

**CIDR (Classless Inter-Domain Routing)** notation expresses the subnet mask as a **slash + number of network bits**, e.g. `/24` means the first 24 bits are the network portion.

| CIDR | Subnet Mask | Binary (last relevant octet) |
|---|---|---|
| /8 | 255.0.0.0 | 00000000 |
| /16 | 255.255.0.0 | 00000000 |
| /24 | 255.255.255.0 | 00000000 |
| /25 | 255.255.255.128 | 10000000 |
| /26 | 255.255.255.192 | 11000000 |
| /27 | 255.255.255.224 | 11100000 |
| /28 | 255.255.255.240 | 11110000 |
| /29 | 255.255.255.248 | 11111000 |
| /30 | 255.255.255.252 | 11111100 |
| /32 | 255.255.255.255 | 11111111 (single host) |

## 4. Full CIDR Reference Table

| CIDR | Subnet Mask | Total Addresses | Usable Hosts |
|---|---|---|---|
| /8 | 255.0.0.0 | 16,777,216 | 16,777,214 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

**Formula:** Usable hosts = 2^(host bits) − 2 (subtracting the network address and broadcast address).

## 5. Network Address, Host Range, and Broadcast Address

Every subnet has three important reference points:

| Term | Description | Bit Pattern |
|---|---|---|
| Network Address | Identifies the subnet itself, not assignable to a device | All host bits = 0 |
| Usable Host Range | The addresses that can actually be assigned to devices | Between network and broadcast |
| Broadcast Address | Used to send to every device on the subnet | All host bits = 1 |

## 6. Worked Example — /26 Subnet

Given `192.168.10.64/26`:

- Subnet mask: `255.255.255.192`
- Block size (increment): `256 − 192 = 64`
- **Network Address:** `192.168.10.64`
- **Usable Host Range:** `192.168.10.65` – `192.168.10.126`
- **Broadcast Address:** `192.168.10.127`
- **Next subnet starts at:** `192.168.10.128`

## 7. Worked Example — Splitting a /24 into Subnets

Splitting `192.168.1.0/24` into 4 equal subnets requires borrowing 2 host bits → `/26`:

| Subnet | Network Address | Usable Range | Broadcast |
|---|---|---|---|
| 1 | 192.168.1.0/26 | .1 – .62 | .63 |
| 2 | 192.168.1.64/26 | .65 – .126 | .127 |
| 3 | 192.168.1.128/26 | .129 – .190 | .191 |
| 4 | 192.168.1.192/26 | .193 – .254 | .255 |

**Rule of thumb:** each time you borrow **1 more bit** for the network, the number of subnets **doubles** and the hosts per subnet **halves**.

## 8. Quick Subnetting Method ("Magic Number" Trick)

1. Find the **interesting octet** — the octet where the mask isn't 255 or 0.
2. Calculate the **block size**: `256 − (mask value in that octet)`.
3. List subnet boundaries by adding the block size repeatedly, starting from 0.

Example: mask `255.255.255.224` (/27) → interesting octet = 4th, mask value = 224 → block size = `256 − 224 = 32` → subnets: `.0, .32, .64, .96, .128, .160, .192, .224`.

## 9. Default Masks by Class (Legacy Reference)

| Class | Default Mask | CIDR |
|---|---|---|
| A | 255.0.0.0 | /8 |
| B | 255.255.0.0 | /16 |
| C | 255.255.255.0 | /24 |

Classful defaults are largely historical — CIDR/VLSM (Variable Length Subnet Masking) is used in practice to allocate address space efficiently rather than fixed class boundaries.

## 10. VLSM (Variable Length Subnet Masking)

VLSM allows different subnets within the same network to use **different mask lengths**, so address space isn't wasted. Example: a point-to-point WAN link only needs 2 usable hosts, so it's given a `/30` (2 usable), while an office LAN needing 100 hosts gets a `/25` (126 usable) — both carved from the same larger block instead of forcing every subnet to the same fixed size.

## 11. Why Subnetting Matters

- **Efficient address allocation** — avoids wasting large blocks on small networks.
- **Broadcast domain control** — smaller subnets = smaller broadcast domains = better performance.
- **Security segmentation** — isolates departments/functions (often paired with VLANs, see [[VLAN]]).
- **Simplified routing** — well-planned subnets allow route summarization/aggregation.

## 12. Interview Questions

1. What does a subnet mask define? → **Which portion of an IP address is the network vs the host**
2. What does /24 mean in CIDR notation? → **The first 24 bits are the network portion (255.255.255.0)**
3. How do you calculate usable hosts in a subnet? → **2^(host bits) − 2**
4. What is the broadcast address of 192.168.10.64/26? → **192.168.10.127**
5. What does VLSM allow that classful/fixed subnetting doesn't? → **Different subnet sizes within the same network for efficient allocation**
6. If you borrow one more bit for subnetting, what happens to the number of subnets and hosts? → **Subnets double, hosts per subnet halve**

## 13. Key Points

- Subnet mask = 32-bit value separating network bits (1s) from host bits (0s).
- CIDR notation (`/24`, `/26`, etc.) is the standard shorthand for subnet masks.
- Usable hosts = 2^(host bits) − 2; network and broadcast addresses are never assignable.
- Block size = 256 − mask value in the interesting octet — the fastest way to calculate subnet boundaries by hand.
- VLSM lets different subnets use different mask sizes for efficient address use.

---
*Related: [[IP_Address]], [[IPv4]], [[VLAN]], [[Routing]]*
