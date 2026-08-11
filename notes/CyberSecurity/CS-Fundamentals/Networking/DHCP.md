## 1. Introduction

**DHCP (Dynamic Host Configuration Protocol)** automatically assigns IP addresses and other network configuration parameters (subnet mask, default gateway, DNS servers) to devices on a network, eliminating the need for manual configuration. It uses **UDP port 67** (server) and **UDP port 68** (client).

## 2. Why DHCP Exists

Manually assigning IPs to every device doesn't scale and is error-prone (duplicate IPs, misconfigured subnets). DHCP centralizes address management, ensures consistency, and lets devices join networks with zero manual setup.

## 3. What DHCP Assigns

- IP address
- Subnet mask
- Default gateway
- DNS server(s)
- Lease duration
- Optionally: NTP server, domain name, TFTP server (PXE boot), WINS server, etc. (all via DHCP Options)

## 4. The DORA Process

DHCP uses a 4-step handshake, commonly remembered as **DORA**:

1. **Discover** — Client broadcasts `DHCPDISCOVER` (src 0.0.0.0, dst 255.255.255.255) looking for any DHCP server.
2. **Offer** — Server(s) respond with `DHCPOFFER`, proposing an IP address and configuration.
3. **Request** — Client broadcasts `DHCPREQUEST`, formally requesting the offered IP (broadcast so other DHCP servers know their offers were declined).
4. **Acknowledge** — Server responds with `DHCPACK`, confirming the lease and finalizing configuration.

```
Client                          Server
  |----- DHCPDISCOVER -------->|  (broadcast)
  |<---- DHCPOFFER -------------|
  |----- DHCPREQUEST --------->|  (broadcast)
  |<---- DHCPACK ---------------|
```

## 5. Lease Renewal

- Each IP assignment has a **lease time**.
- At **50%** of the lease time, the client sends a unicast `DHCPREQUEST` to the original server to renew (**T1** timer).
- If that fails, at **87.5%** of the lease time, the client broadcasts a renewal request to any server (**T2** timer).
- If the lease fully expires with no renewal, the client must restart the DORA process from scratch.

## 6. DHCP Release

A client can voluntarily give up its lease early with `DHCPRELEASE` (e.g., `ipconfig /release` on Windows), freeing the address for reassignment.

## 7. DHCP Relay Agent

DHCP relies on broadcasts, which don't cross routers/subnets by default. A **DHCP Relay Agent** (often the router itself, via `ip helper-address` on Cisco devices) listens for broadcasts on a local subnet and forwards them as unicast to a DHCP server on a different subnet — allowing one centralized DHCP server to serve multiple subnets.

## 8. Static (Reserved) vs Dynamic Assignment

| Type | Description |
|---|---|
| Dynamic Allocation | IP assigned from a pool for the lease duration, may change over time |
| Automatic Allocation | Server permanently assigns an IP from the pool the first time |
| Static / Reservation | Server always assigns the same IP to a specific MAC address (via DHCP reservation) |
| Manual | Admin manually configures the device, bypassing DHCP entirely |

## 9. APIPA (Automatic Private IP Addressing)

If a client can't reach a DHCP server, Windows devices self-assign an address in **169.254.0.0/16** so they can at least communicate on the local link — a strong troubleshooting signal that DHCP failed.

## 10. DHCP Security Risks

| Threat | Description |
|---|---|
| Rogue DHCP Server | An unauthorized/malicious DHCP server hands out bad configs (e.g., wrong gateway/DNS) to redirect or intercept traffic (MITM) |
| DHCP Starvation | Attacker floods a real DHCP server with fake DISCOVER requests using spoofed MACs, exhausting the address pool so legitimate clients can't get an IP |
| DHCP Spoofing | Attacker responds to DISCOVER broadcasts faster than the legitimate server, becoming a MITM |

**Mitigations:**
- **DHCP Snooping** (switch feature) — only trusts DHCP server responses on designated "trusted" ports, blocks rogue offers on "untrusted" ports.
- Port security to limit MAC addresses per port (mitigates starvation).
- Network monitoring for unexpected DHCP server traffic.

## 11. Practical Commands

```bash
# Windows
ipconfig /release
ipconfig /renew
ipconfig /all

# Linux
sudo dhclient -r      # release
sudo dhclient         # renew/request
```

## 12. Interview Questions

1. What does DHCP stand for? → **Dynamic Host Configuration Protocol**
2. What ports does DHCP use? → **UDP 67 (server), UDP 68 (client)**
3. What does DORA stand for? → **Discover, Offer, Request, Acknowledge**
4. What happens if a client can't reach a DHCP server on Windows? → **It self-assigns an APIPA address (169.254.x.x)**
5. What switch feature prevents rogue DHCP servers? → **DHCP Snooping**
6. What attack exhausts the DHCP address pool? → **DHCP Starvation**

## 13. Key Points

- DHCP automates IP configuration; uses UDP ports 67/68.
- Process = **DORA**: Discover → Offer → Request → Acknowledge.
- Leases are renewed at 50% (T1) and 87.5% (T2) of lease time.
- DHCP Relay forwards broadcasts across subnets to a central server.
- Rogue DHCP servers and DHCP starvation are key attack vectors — mitigated with DHCP Snooping.

---
*Related: [[DNS]], [[IPv4]], [[Ports]]*
