## 1. Introduction

**Routing** is the process of selecting a path for traffic to travel between networks, performed by **routers** operating at **Layer 3 (Network)** of the OSI model. Routers use **routing tables** to decide the best next hop for each packet based on its destination IP address.

## 2. How Routing Works

1. A router receives a packet.
2. It examines the **destination IP address**.
3. It consults its **routing table** to find the best matching route (longest prefix match).
4. It forwards the packet out the corresponding interface to the next hop.
5. If no match exists and there's no default route, the packet is dropped (and typically an ICMP "destination unreachable" is sent back).

## 3. Routing Table Components

| Field | Description |
|---|---|
| Destination Network | The network/subnet this route applies to |
| Subnet Mask / Prefix | Defines the size of that network |
| Next Hop | The IP of the next router to forward to |
| Interface | The local interface to send the packet out of |
| Metric | Cost value used to choose between multiple routes to the same destination |
| Administrative Distance (AD) | Trustworthiness ranking used to choose between different routing sources |

## 4. Static vs Dynamic Routing

| Aspect | Static Routing | Dynamic Routing |
|---|---|---|
| Configuration | Manually configured by admin | Automatically learned via routing protocols |
| Scalability | Poor for large/changing networks | Scales well, adapts automatically |
| Overhead | No CPU/bandwidth overhead | Uses CPU and bandwidth for updates |
| Convergence | Instant (but manual) if a link fails, admin must intervene | Automatically reconverges around failures |
| Use case | Small networks, stub networks, specific security-controlled paths | Large, dynamic, enterprise/ISP networks |

## 5. Administrative Distance (Trustworthiness)

When multiple sources offer a route to the same destination, the router picks the one with the **lowest Administrative Distance**:

| Source | Default AD (Cisco) |
|---|---|
| Directly connected | 0 |
| Static route | 1 |
| EIGRP (internal) | 90 |
| OSPF | 110 |
| RIP | 120 |
| External EIGRP | 170 |
| Unknown/unreachable | 255 |

## 6. Interior vs Exterior Routing Protocols

| Type | Scope | Examples |
|---|---|---|
| IGP (Interior Gateway Protocol) | Within a single autonomous system (AS) | RIP, OSPF, EIGRP, IS-IS |
| EGP (Exterior Gateway Protocol) | Between different autonomous systems (the internet backbone) | BGP |

## 7. Routing Protocol Categories

| Category | How it Works | Examples |
|---|---|---|
| Distance-Vector | Shares entire routing table with neighbors; picks path with lowest "distance" (hop count) | RIP, EIGRP (hybrid) |
| Link-State | Each router builds a full map of the network topology and calculates shortest paths independently (Dijkstra's algorithm) | OSPF, IS-IS |
| Path-Vector | Tracks the full AS path a route has traversed to prevent loops; policy-driven | BGP |

## 8. Common Routing Protocols

- **RIP (Routing Information Protocol)** — Distance-vector, metric = hop count, max 15 hops, slow convergence. Largely legacy.
- **OSPF (Open Shortest Path First)** — Link-state, metric = cost (based on bandwidth), fast convergence, widely used in enterprise networks.
- **EIGRP (Enhanced Interior Gateway Routing Protocol)** — Cisco-proprietary (later partially open), hybrid distance-vector, uses DUAL algorithm for fast convergence.
- **BGP (Border Gateway Protocol)** — Path-vector, the routing protocol of the **internet backbone**, connects autonomous systems, highly policy-configurable.

## 9. Default Route

A **default route** (`0.0.0.0/0` in IPv4) is the "route of last resort" — used when no more specific match exists in the routing table. Commonly points toward the internet gateway on edge routers/end networks.

## 10. Routing Metrics by Protocol

| Protocol | Metric Basis |
|---|---|
| RIP | Hop count |
| OSPF | Cost (inversely proportional to bandwidth) |
| EIGRP | Bandwidth, delay, reliability, load (composite formula) |
| BGP | AS-path length, policies, local preference, MED |

## 11. Convergence

**Convergence** is the time it takes for all routers in a network to agree on the current topology after a change (link failure, new route, etc.). Link-state protocols (OSPF) generally converge faster than distance-vector protocols (RIP) because each router has full topology visibility instead of relying on gradual table exchanges.

## 12. Practical Commands

```bash
# View routing table
# Linux
ip route
route -n

# Windows
route print

# Cisco IOS
show ip route
```

## 13. Interview Questions

1. At which OSI layer does routing occur? → **Layer 3 (Network)**
2. What's the difference between static and dynamic routing? → **Static: manually configured; Dynamic: automatically learned via protocols**
3. Which is more trusted by default — OSPF or a static route? → **Static route (AD 1) is more trusted than OSPF (AD 110)**
4. Name a link-state routing protocol. → **OSPF**
5. What protocol runs the internet backbone between autonomous systems? → **BGP**
6. What does a default route (0.0.0.0/0) represent? → **The route used when no more specific match exists**

## 14. Key Points

- Routing = forwarding packets between networks at Layer 3, guided by a routing table.
- Static routing = manual; dynamic routing = automatic via protocols (RIP, OSPF, EIGRP, BGP).
- Administrative Distance decides which source wins when multiple routes exist; metric decides the best path within one protocol.
- IGPs (OSPF, EIGRP, RIP) work within an AS; EGP (BGP) works between autonomous systems.
- Link-state protocols converge faster than distance-vector protocols.

---
*Related: [[Switching]], [[NAT]], [[TCP_IP_Model]]*
