## 1. Introduction

The **TCP/IP Model** (also called the **Internet Protocol Suite**) is the practical, real-world networking model that the entire internet runs on. It predates the OSI model and is simpler — **4 layers** instead of 7 — but maps conceptually onto it. Where OSI is a theoretical *reference* model, TCP/IP is the *implemented* model.

## 2. The Four Layers

| Layer | Function |
|---|---|
| 4. Application | User-facing protocols and services |
| 3. Transport | End-to-end communication, reliability |
| 2. Internet | Logical addressing and routing |
| 1. Network Access (Link) | Physical transmission and local addressing |

Some textbooks split this into 5 layers (Application, Transport, Internet, Data Link, Physical) to align more directly with OSI — both versions are widely accepted.

## 3. Mapping to the OSI Model

| OSI Layer | TCP/IP Layer |
|---|---|
| 7 Application | Application |
| 6 Presentation | Application |
| 5 Session | Application |
| 4 Transport | Transport |
| 3 Network | Internet |
| 2 Data Link | Network Access |
| 1 Physical | Network Access |

TCP/IP merges OSI's top three layers into one **Application** layer, and merges the bottom two into one **Network Access** layer.

## 4. Layer 4 — Application

Provides protocols that applications use directly to communicate across the network.

**Protocols:** HTTP/HTTPS, FTP, SMTP, POP3, IMAP, DNS, DHCP, SSH, Telnet, SNMP.

## 5. Layer 3 — Transport

Provides end-to-end communication between hosts, using **TCP** or **UDP**.

- **TCP** — connection-oriented, reliable, ordered (see [[TCP_Protocol]]).
- **UDP** — connectionless, fast, no delivery guarantee.

PDU: **Segment** (TCP) / **Datagram** (UDP).

## 6. Layer 2 — Internet

Handles logical addressing, routing, and packet forwarding between networks.

**Protocols:** IP (IPv4/IPv6), ICMP, ARP, IGMP, routing protocols (OSPF, BGP, RIP).

PDU: **Packet**.

## 7. Layer 1 — Network Access (Link)

Combines OSI's Data Link + Physical layers — handles framing, MAC addressing, and the actual transmission of bits over physical or wireless media.

**Protocols/technologies:** Ethernet, Wi-Fi (802.11), PPP, ARP (sometimes placed here instead), switches, NICs, cabling.

PDU: **Frame** / **Bits**.

## 8. Data Encapsulation in TCP/IP

As data moves down the stack, each layer wraps it with its own header:

```
Application Data
   → [TCP/UDP Header] Segment/Datagram
      → [IP Header] Packet
         → [Ethernet Header + Trailer] Frame
            → Bits on the wire
```

## 9. Why TCP/IP Won Over OSI

- TCP/IP was implemented and battle-tested (ARPANET) before OSI was finalized.
- Simpler 4-layer design was easier to implement and troubleshoot.
- Open, vendor-neutral, and free — became the de facto standard for the internet.
- OSI remains valuable as a **teaching and troubleshooting reference model**, even though TCP/IP is what's actually deployed.

## 10. TCP/IP vs OSI — Quick Comparison

| Aspect | OSI Model | TCP/IP Model |
|---|---|---|
| Layers | 7 | 4 (sometimes shown as 5) |
| Developed by | ISO | DARPA / DoD |
| Nature | Theoretical reference model | Practical, implemented model |
| Usage | Teaching, troubleshooting | Actual internet protocol suite |
| Layer boundaries | Strict, well-defined | Looser, protocol-driven |

## 11. Interview Questions

1. How many layers does the TCP/IP model have? → **4** (sometimes 5)
2. Which TCP/IP layer combines OSI's Session, Presentation, and Application layers? → **Application**
3. Which layer handles IP addressing and routing? → **Internet**
4. Which protocol suite is actually used on the modern internet? → **TCP/IP**
5. What are the two main Transport layer protocols? → **TCP and UDP**

## 12. Key Points

- TCP/IP has **4 layers**: Application, Transport, Internet, Network Access.
- It is the model actually implemented on the internet; OSI is the teaching reference model.
- Application layer merges OSI layers 5-7; Network Access merges OSI layers 1-2.
- TCP/UDP live at the Transport layer; IP lives at the Internet layer.

---
*Related: [[OSI_MODEL]], [[TCP_Protocol]], [[IPv4]], [[IPv6]]*
