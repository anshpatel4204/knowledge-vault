## 1. Introduction

A **MAC Address (Media Access Control Address)** is a unique 48-bit hardware identifier assigned to a network interface card (NIC), used at **Layer 2 (Data Link)** of the OSI model to identify devices on the **same local network segment**. Unlike IP addresses, MAC addresses are physical/hardware addresses and are (in theory) globally unique and permanent, burned in by the manufacturer.

## 2. Address Format

A MAC address is **48 bits (6 bytes)**, typically written as **six pairs of hexadecimal digits** separated by colons or hyphens:

```
00:1A:2B:3C:4D:5E
00-1A-2B-3C-4D-5E   (Windows style)
```

## 3. Structure — OUI and NIC-Specific Portion

| Portion | Bits | Bytes | Description |
|---|---|---|---|
| OUI (Organizationally Unique Identifier) | First 24 bits | First 3 bytes | Assigned by IEEE to the manufacturer (e.g., Cisco, Intel, Apple) |
| NIC-Specific / Device Identifier | Last 24 bits | Last 3 bytes | Assigned by the manufacturer, unique per device |

```
00:1A:2B  |  3C:4D:5E
 OUI      |  Device-specific
(Manufacturer)
```

You can look up a MAC's manufacturer using its OUI via public IEEE databases — useful in network inventory and forensic investigations.

## 4. Unicast, Multicast, and Broadcast MAC Addresses

The **least significant bit of the first byte** (I/G bit) indicates whether an address is unicast or multicast:

| Type | Description | Example |
|---|---|---|
| Unicast | Identifies a single, specific NIC | 00:1A:2B:3C:4D:5E |
| Multicast | Delivered to a group of interested devices | 01:00:5E:xx:xx:xx (IPv4 multicast mapping) |
| Broadcast | Delivered to every device on the local segment | FF:FF:FF:FF:FF:FF |

The **second-least-significant bit of the first byte** (U/L bit) indicates whether the address is **Universally** administered (factory-set) or **Locally** administered (manually changed/virtualized).

## 5. MAC Address vs IP Address

| Aspect | MAC Address | IP Address |
|---|---|---|
| Layer | Data Link (L2) | Network (L3) |
| Type | Physical/hardware address | Logical/software address |
| Assigned by | Manufacturer (burned into hardware) | DHCP or manual configuration |
| Scope | Local network segment only | Routable across networks (internet) |
| Format | 48-bit hex (00:1A:2B:3C:4D:5E) | 32-bit (IPv4) or 128-bit (IPv6) |
| Changeable | Rarely, but can be spoofed/overridden in software | Frequently, especially with DHCP |

## 6. How MAC Addresses Are Used

- **Switches** use MAC addresses to populate their **MAC address table (CAM table)** and forward frames intelligently (see [[Switching]]).
- **ARP (Address Resolution Protocol)** maps a known IP address to its corresponding MAC address so a frame can actually be delivered on the local segment.
- **DHCP reservations** can bind a specific IP to a device's MAC address for consistent addressing (see [[DHCP]]).
- **Port Security** on switches restricts which MAC addresses are allowed on a given port.

## 7. ARP — The IP-to-MAC Bridge

When a device wants to send a packet to another device on the same subnet, it needs the destination's MAC address:

1. Device broadcasts an **ARP Request**: "Who has 192.168.1.20? Tell 192.168.1.10."
2. The device with that IP replies with an **ARP Reply** containing its MAC address.
3. The sender caches this mapping in its local **ARP table** (`arp -a`) for future use, until it expires.

## 8. MAC Address Spoofing

Because MAC addresses are read from software (the OS reports what the NIC driver tells it), they can be changed/spoofed relatively easily — unlike being cryptographically tied to hardware.

**Legitimate uses:** privacy (randomized MAC on Wi-Fi to avoid tracking), testing, virtualization (VMs get virtual/locally-administered MACs).

**Malicious uses:**
- Bypassing MAC-based access control (e.g., Wi-Fi MAC allow-lists).
- Impersonating a trusted device.
- Evading network-based device tracking/blocking.

## 9. MAC-Related Attacks

| Attack | Description | Mitigation |
|---|---|---|
| MAC Flooding | Flooding a switch with fake source MACs to overflow its CAM table, forcing it to broadcast all traffic (enabling sniffing) | Port Security (limit MACs per port) |
| ARP Spoofing / Poisoning | Sending forged ARP replies to associate an attacker's MAC with another device's IP (commonly the gateway), enabling MITM | Dynamic ARP Inspection (DAI), static ARP entries for critical hosts |
| MAC Spoofing | Changing your own MAC to impersonate an authorized device | 802.1X port-based authentication, monitoring for duplicate MACs |

## 10. Practical Commands

```bash
# View MAC address
# Windows
ipconfig /all
getmac

# Linux / macOS
ip link show
ifconfig

# View ARP table
arp -a
```

## 11. Interview Questions

1. How many bits is a MAC address? → **48 bits**
2. What does OUI identify? → **The manufacturer of the network interface** (first 24 bits)
3. What is the broadcast MAC address? → **FF:FF:FF:FF:FF:FF**
4. What protocol resolves an IP address to a MAC address? → **ARP**
5. Is a MAC address a logical or physical address? → **Physical (hardware) address**
6. What attack overflows a switch's CAM table using fake MACs? → **MAC flooding**

## 12. Key Points

- MAC address = 48-bit hardware address, used at Layer 2 for local delivery.
- Structure: first 24 bits = OUI (manufacturer), last 24 bits = device-specific.
- Broadcast MAC = FF:FF:FF:FF:FF:FF.
- ARP bridges IP (Layer 3) and MAC (Layer 2) addressing.
- MAC addresses can be spoofed in software — not a strong security control on their own.
- Key attacks: MAC flooding, ARP spoofing/poisoning, MAC spoofing.

---
*Related: [[IP_Address]], [[Switching]], [[OSI_MODEL]]*
