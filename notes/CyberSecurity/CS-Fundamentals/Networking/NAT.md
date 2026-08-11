## 1. Introduction

**NAT (Network Address Translation)** translates private IP addresses used inside a local network into a public IP address (and vice versa) when communicating with the internet. It operates on routers/firewalls at the edge of a network and was primarily created to solve **IPv4 address exhaustion**, while also providing a side benefit of hiding internal network topology.

## 2. Why NAT Exists

- **IPv4 scarcity** — allows many private devices to share a small number (often one) of public IP addresses.
- **Security through obscurity** — internal IP addressing scheme is hidden from external hosts.
- **Flexibility** — internal addressing can change without needing new public IP allocations.

## 3. Types of NAT

| Type | Description |
|---|---|
| Static NAT | One private IP is permanently mapped to one public IP (1:1) — used for servers that need to be consistently reachable |
| Dynamic NAT | Private IPs are mapped to a pool of public IPs on a first-come, first-served basis |
| PAT (Port Address Translation) / NAT Overload | Many private IPs share **one** public IP, differentiated by port number — the most common form, used in virtually all home/office routers |

## 4. How PAT Works (Most Common Form)

1. Internal host `192.168.1.10:5555` sends a request to `8.8.8.8:443`.
2. The router/firewall translates the source to its public IP with a unique port, e.g. `203.0.113.5:40001`.
3. The router records this mapping in a **NAT translation table**.
4. The response comes back to `203.0.113.5:40001`.
5. The router looks up the table, translates it back to `192.168.1.10:5555`, and forwards it internally.

**NAT Translation Table Example:**

| Inside Local | Inside Global | Outside |
|---|---|---|
| 192.168.1.10:5555 | 203.0.113.5:40001 | 8.8.8.8:443 |
| 192.168.1.11:6001 | 203.0.113.5:40002 | 1.1.1.1:443 |

## 5. NAT Terminology (Cisco Convention)

| Term | Meaning |
|---|---|
| Inside Local | Private IP of the internal host, as seen inside the network |
| Inside Global | Public/translated IP of the internal host, as seen outside |
| Outside Local | How the external host's IP appears inside the local network |
| Outside Global | The real public IP of the external host |

## 6. NAT vs PAT — Quick Comparison

| Aspect | Static/Dynamic NAT | PAT |
|---|---|---|
| Mapping | 1:1 (or pool-based) | Many:1 |
| Public IPs needed | One per internal host (static) or per concurrent session (dynamic) | Just one for the whole network |
| Use case | Publishing internal servers | Standard internet access for LAN clients |

## 7. NAT and Inbound Connections — Port Forwarding

Because PAT hides internal hosts behind one public IP, unsolicited **inbound** connections can't reach internal devices by default (this is a security benefit). To expose an internal service (e.g., a web server) to the internet, admins configure **port forwarding** — a static rule mapping `PublicIP:Port` → `PrivateIP:Port`.

## 8. Advantages and Disadvantages

**Advantages:**
- Conserves public IPv4 addresses.
- Hides internal network structure from outside.
- Simplifies internal renumbering without needing new public allocations.

**Disadvantages:**
- Breaks true end-to-end connectivity (a core internet design principle).
- Complicates protocols that embed IP addresses in payloads (e.g., some VoIP/SIP scenarios) — needs Application Layer Gateways (ALGs).
- Adds a layer of state that must be maintained (NAT tables), which can be a scaling/performance concern.
- Not a substitute for a firewall, though it has a firewall-like side effect of blocking unsolicited inbound traffic.

## 9. NAT and IPv6

IPv6's vast address space removes the primary motivation for NAT (address scarcity). While **NAT66/NPTv6** exists for specific renumbering scenarios, IPv6 networks generally favor proper firewalling over NAT for security, since every device can have a globally routable address.

## 10. NAT Traversal

Some applications (VoIP, peer-to-peer, VPNs) struggle behind NAT since both ends may be behind different NAT devices. Techniques like **STUN**, **TURN**, and **ICE** help establish connections through NAT for such use cases.

## 11. Interview Questions

1. What problem does NAT primarily solve? → **IPv4 address exhaustion**
2. What's the most common form of NAT used in home routers? → **PAT (Port Address Translation) / NAT Overload**
3. What's the difference between Static NAT and PAT? → **Static: 1:1 IP mapping; PAT: many private IPs share one public IP via ports**
4. What is "Inside Local" in Cisco NAT terminology? → **The private IP of an internal host**
5. How do you expose an internal server behind PAT to the internet? → **Port forwarding**
6. Why is NAT less necessary with IPv6? → **IPv6's address space is large enough that every device can have a public address**

## 12. Key Points

- NAT translates private ↔ public IPs, primarily to conserve IPv4 addresses.
- Three types: **Static** (1:1), **Dynamic** (pool-based), **PAT** (many:1, most common).
- PAT differentiates hosts using port numbers, tracked in a NAT translation table.
- NAT indirectly blocks unsolicited inbound traffic — port forwarding is needed to expose internal services.
- IPv6 reduces reliance on NAT due to its abundant address space.

---
*Related: [[IPv4]], [[Routing]], [[VPN]]*
