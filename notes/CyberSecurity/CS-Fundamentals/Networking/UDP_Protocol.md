## 1. Introduction

**UDP (User Datagram Protocol)** is a connectionless, lightweight Transport layer protocol defined in RFC 768. Unlike TCP, it provides no guarantees of delivery, ordering, or duplicate protection — in exchange, it offers minimal overhead and low latency, making it the protocol of choice for applications where speed matters more than perfect reliability.

## 2. Key Characteristics

- **Connectionless** — no handshake; the sender simply starts transmitting datagrams.
- **Unreliable** — no acknowledgments, no automatic retransmission of lost datagrams.
- **Unordered** — datagrams may arrive out of order; UDP does not reorder them.
- **No flow/congestion control** — a fast sender can overwhelm a slow receiver or the network; any reliability/ordering must be built by the application itself if needed.
- **Low overhead** — an 8-byte header vs TCP's 20-60 bytes, and no handshake/teardown round trips.
- **Message-oriented** — preserves datagram boundaries (each `send()` call maps to one discrete datagram), unlike TCP's continuous byte stream.

## 3. UDP Header Format

A UDP header is a fixed **8 bytes**:

| Field | Size | Description |
|---|---|---|
| Source Port | 16 bits | Sending application's port |
| Destination Port | 16 bits | Receiving application's port |
| Length | 16 bits | Length of the UDP header + data |
| Checksum | 16 bits | Error-checking for header and data (optional in IPv4, mandatory in IPv6) |

Compare this to TCP's 20-60 byte header (see [[TCP_Protocol]]) — the simplicity is exactly why UDP is fast.

## 4. TCP vs UDP

| Aspect | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed delivery, retransmits lost segments | Best-effort — no delivery guarantee |
| Ordering | Guaranteed via sequence numbers | Not guaranteed |
| Speed | Slower (handshake, ACKs, congestion control overhead) | Faster, minimal overhead |
| Header size | 20-60 bytes | 8 bytes (fixed) |
| Flow/Congestion control | Yes | No |
| Data boundaries | Byte stream (no message boundaries) | Preserves datagram/message boundaries |
| Use cases | Web, email, file transfer, database connections | DNS, DHCP, VoIP, video streaming, gaming, SNMP |

## 5. Why Applications Choose UDP

- **Real-time media** (VoIP, video calls, live streaming) — a late-arriving retransmitted packet is worse than a dropped one; better to skip and move on than stall waiting for a retransmit.
- **DNS queries** — small, simple request/response; retransmission is handled at the application layer with a timeout/retry if needed (see [[DNS]]).
- **DHCP** — relies on broadcast, which fits UDP's connectionless model naturally (see [[DHCP]]).
- **Online gaming** — needs the lowest possible latency; occasional lost position updates are tolerable, waiting for retransmission is not.
- **SNMP** — simple periodic polling of network device status, doesn't need TCP's overhead.

## 6. When Reliability Is Still Needed Over UDP

Some applications need UDP's speed but also need reliability — they implement their own reliability layer **on top of** UDP rather than using TCP:

| Example | Approach |
|---|---|
| QUIC (used in HTTP/3) | Implements its own reliability, ordering, and congestion control over UDP, while avoiding TCP's head-of-line blocking |
| Custom game networking | Selective retransmission only for critical data (e.g., player actions), not for less critical continuous state (e.g., position interpolation) |
| TFTP | Simple stop-and-wait acknowledgment scheme built on top of UDP |

## 7. Common UDP Ports

| Port | Service |
|---|---|
| 53 | DNS (queries) |
| 67/68 | DHCP |
| 69 | TFTP |
| 123 | NTP |
| 161/162 | SNMP |
| 500 | IKE (IPSec key exchange) |
| 514 | Syslog |
| 1900 | SSDP/UPnP |
| 3478 | STUN/TURN (NAT traversal) |

## 8. UDP Security Considerations

| Concern | Description |
|---|---|
| Spoofing | No handshake means the source IP in a UDP packet is trivial to forge — no verification that the sender is who they claim to be |
| Amplification/Reflection DDoS | Attackers spoof a victim's IP and send small requests to open UDP services (DNS, NTP, memcached) that reply with much larger responses, flooding the victim — UDP's connectionless nature makes this possible in a way TCP's handshake prevents |
| No built-in encryption/authentication | Applications using raw UDP must add their own security (e.g., DTLS — Datagram TLS — for encrypted UDP traffic) |

**Mitigations:** rate limiting, disabling/restricting open UDP services (open DNS resolvers, NTP monlist), ingress/egress filtering (BCP38) to block spoofed source addresses, DTLS or application-layer encryption where confidentiality is needed.

## 9. Practical Commands

```bash
# Check listening UDP ports
ss -ulnp
netstat -ulnp

# Test UDP connectivity
nc -u -zv <host> <port>
```

## 10. Interview Questions

1. Is UDP connection-oriented or connectionless? → **Connectionless**
2. How large is a UDP header? → **8 bytes (fixed)**
3. Why do real-time applications like VoIP prefer UDP over TCP? → **A dropped packet is preferable to the latency of waiting for TCP retransmission of an old packet**
4. Why is UDP more susceptible to spoofing-based amplification DDoS than TCP? → **No handshake means the source IP is never verified before a large response is sent**
5. Name a protocol that adds reliability on top of UDP instead of using TCP. → **QUIC (HTTP/3)**
6. What does DTLS provide for UDP traffic? → **Encryption and authentication, similar to what TLS provides for TCP**

## 11. Key Points

- UDP = connectionless, unreliable, unordered, minimal 8-byte header — built for **speed over guarantees**.
- No handshake, no retransmission, no congestion control — reliability (if needed) is the application's responsibility.
- Ideal for DNS, DHCP, VoIP, streaming, gaming, SNMP.
- **Spoofing and amplification DDoS** are UDP's key security weaknesses due to the lack of a handshake.
- Modern protocols like **QUIC** build custom reliability on top of UDP to get both speed and guarantees.

---
*Related: [[TCP_Protocol]], [[TCP_IP_Model]], [[Ports]], [[DNS]], [[DHCP]]*
