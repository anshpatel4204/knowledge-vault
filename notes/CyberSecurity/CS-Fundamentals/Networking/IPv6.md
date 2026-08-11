## 1. Introduction

**IPv6 (Internet Protocol version 6)** is the successor to IPv4, designed to solve address exhaustion and improve routing efficiency, security, and configuration. It uses **128-bit addresses**, providing approximately **340 undecillion** (3.4 x 10^38) unique addresses — effectively limitless for the foreseeable future.

## 2. Address Format

IPv6 addresses are **128 bits**, written as **eight groups of four hexadecimal digits** separated by colons:

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

**Shortening rules:**

- Leading zeros in a group can be omitted: `0db8` → `db8`
- One consecutive run of all-zero groups can be replaced with `::` (only once per address)

```
2001:0db8:0000:0000:0000:0000:1428:57ab
→ 2001:db8::1428:57ab
```

## 3. IPv6 Address Types

| Type | Description | Example Prefix |
|---|---|---|
| Unicast | One-to-one, identifies a single interface | Global: 2000::/3 |
| Multicast | One-to-many, delivered to all members of a group | ff00::/8 |
| Anycast | One-to-nearest, delivered to the closest of a group of interfaces | (same as unicast range) |
| Link-Local | Auto-configured, not routable beyond the local link | fe80::/10 |
| Loopback | Local host testing | ::1 |
| Unspecified | Absence of an address (used before one is assigned) | :: |
| Unique Local | Private addressing, like RFC 1918 for IPv6 | fc00::/7 |

Note: **IPv6 has no broadcast** — multicast and anycast replace its functionality entirely.

## 4. Address Structure

An IPv6 unicast address is typically split into:

| Part | Bits | Purpose |
|---|---|---|
| Global Routing Prefix | 48 | Assigned by ISP/registry, identifies the network |
| Subnet ID | 16 | Defines subnets within the organization |
| Interface ID | 64 | Identifies the specific host/interface |

## 5. Address Autoconfiguration

- **SLAAC (Stateless Address Autoconfiguration)** — device generates its own address using the network prefix (from router advertisements) + an interface identifier (often derived from the MAC address, or randomized for privacy).
- **DHCPv6** — stateful configuration similar to DHCP for IPv4, used when more control is needed (e.g., DNS server assignment).
- **Router Advertisements (RA)** — routers periodically send prefix and configuration info via ICMPv6, replacing much of DHCP's role from IPv4.

## 6. IPv6 vs IPv4

| Aspect | IPv4 | IPv6 |
|---|---|---|
| Address length | 32 bits | 128 bits |
| Address format | Dotted decimal | Hexadecimal, colon-separated |
| Total addresses | ~4.3 billion | ~340 undecillion |
| Header | Complex, variable, includes checksum | Simplified, fixed 40 bytes, no checksum |
| Broadcast | Yes | No (uses multicast) |
| Configuration | Manual / DHCP | SLAAC / DHCPv6 |
| NAT | Common (address scarcity) | Generally unnecessary |
| Built-in security | Optional (IPSec add-on) | IPSec support built into the standard |
| Fragmentation | Done by routers and hosts | Done only by the sending host |

## 7. IPv6 Header (Simplified vs IPv4)

IPv6 removed several IPv4 header fields (header checksum, fragmentation-related fields moved to extension headers) to speed up router processing. Key fields: **Version, Traffic Class, Flow Label, Payload Length, Next Header, Hop Limit** (equivalent to TTL), **Source/Destination Address**.

## 8. Transition Mechanisms (IPv4 ↔ IPv6 Coexistence)

Since IPv6 isn't fully deployed everywhere, transition techniques bridge the two:

| Mechanism | Description |
|---|---|
| Dual Stack | Device/network runs both IPv4 and IPv6 simultaneously |
| Tunneling (6to4, Teredo, 6in4) | Encapsulates IPv6 packets inside IPv4 to cross IPv4-only networks |
| NAT64 / DNS64 | Translates between IPv6-only and IPv4-only networks |

## 9. Security Considerations

- IPv6's massive address space makes traditional **network scanning** (sweeping every host) impractical, but it also complicates asset inventory and logging.
- **Link-local addresses** and **SLAAC** introduce new attack surfaces (e.g., rogue Router Advertisements — "RA spoofing").
- Many organizations run **dual stack** without realizing IPv6 is active, creating unmonitored shadow attack paths — a common misconfiguration flagged in security audits.
- IPSec being built-in doesn't mean it's automatically used — it must still be explicitly configured.

## 10. Interview Questions

1. How many bits is an IPv6 address? → **128 bits**
2. What replaces broadcast in IPv6? → **Multicast**
3. What is the IPv6 loopback address? → **::1**
4. What does SLAAC stand for? → **Stateless Address Autoconfiguration**
5. What is the link-local prefix in IPv6? → **fe80::/10**
6. Name one transition mechanism between IPv4 and IPv6. → **Dual stack / tunneling / NAT64**

## 11. Key Points

- IPv6 = 128-bit address, written in hex groups separated by colons.
- `::` compresses one run of consecutive zero groups; can only be used once.
- No broadcast in IPv6 — multicast and anycast take over.
- SLAAC and DHCPv6 handle address assignment; Router Advertisements distribute prefixes.
- Dual stack, tunneling, and NAT64 enable IPv4/IPv6 coexistence during migration.

---
*Related: [[IPv4]], [[TCP_IP_Model]]*
