## 1. Introduction

A **VPN (Virtual Private Network)** creates an encrypted, authenticated tunnel across an untrusted network (typically the public internet), allowing traffic to travel as if it were on a private, secure network. VPNs protect **confidentiality** and **integrity** of data in transit and can also mask the user's real IP address/location.

## 2. Why Use a VPN

- Securely connect remote workers to a corporate network.
- Securely connect two office networks/sites over the internet (replacing expensive leased lines).
- Protect traffic on untrusted networks (public Wi-Fi).
- Bypass geographic restrictions / obscure browsing origin (consumer use case).
- Extend private addressing across a public network without direct exposure.

## 3. Types of VPN (By Purpose)

| Type | Description |
|---|---|
| Remote Access VPN | An individual device connects to a private network remotely (e.g., employee working from home) |
| Site-to-Site VPN | Connects two entire networks (e.g., two office branches) permanently over the internet |
| Client-to-Site VPN | Synonym often used for remote access VPN |
| Extranet VPN | Site-to-site style VPN extended to trusted external partners |

## 4. Tunneling Concept

VPNs work by **encapsulating** one packet inside another — the original packet (often with a private IP) is wrapped in a new packet with public, routable addressing, then encrypted, sent across the internet, and unwrapped/decrypted at the other end.

```
[Original Packet] → [Encrypted] → [New Outer Header + Encrypted Payload] → sent over internet → decrypted at destination
```

## 5. Key VPN Protocols

### IPSec (Internet Protocol Security)
- A **suite** of protocols (not a single protocol) providing encryption, integrity, and authentication at the Network layer.
- **AH (Authentication Header)** — provides integrity and authentication, no encryption.
- **ESP (Encapsulating Security Payload)** — provides encryption plus optional integrity/authentication (the one almost always used in practice).
- **IKE (Internet Key Exchange)** — negotiates and manages the security associations (SAs) and keys.
- Two modes: **Transport mode** (encrypts payload only, used host-to-host) and **Tunnel mode** (encrypts entire original packet, used for site-to-site VPNs).

### SSL/TLS VPN
- Operates at a higher layer, often via a browser or lightweight client (no need for a dedicated network-layer client).
- Commonly used for remote access to specific applications/portals rather than full network access.
- Uses standard HTTPS port **443**, which makes it firewall-friendly (less likely to be blocked than IPSec).

### WireGuard
- Modern, lightweight, high-performance VPN protocol using state-of-the-art cryptography (Curve25519, ChaCha20).
- Much smaller codebase than IPSec/OpenVPN, easier to audit, faster handshakes, gaining rapid adoption.

### OpenVPN
- Open-source, SSL/TLS-based VPN protocol, highly configurable, runs over UDP or TCP, widely supported across platforms.

### PPTP / L2TP (Legacy)
- **PPTP (Point-to-Point Tunneling Protocol)** — old, fast, but cryptographically broken/insecure by modern standards; avoid.
- **L2TP (Layer 2 Tunneling Protocol)** — provides tunneling but no encryption on its own; almost always paired with IPSec as **L2TP/IPSec**.

## 6. VPN Protocol Comparison

| Protocol | Layer | Encryption | Speed | Notes |
|---|---|---|---|---|
| IPSec | Network (L3) | Strong | Fast | Standard for site-to-site VPNs |
| SSL/TLS VPN | Transport/App | Strong | Moderate | Great for remote access, firewall-friendly (port 443) |
| WireGuard | Network (L3) | Strong, modern | Very Fast | Lightweight, modern default choice |
| OpenVPN | Application | Strong | Moderate | Flexible, widely compatible |
| PPTP | Data Link | Weak/Broken | Fast | Deprecated, insecure |
| L2TP/IPSec | Data Link + Network | Strong (via IPSec) | Moderate | L2TP alone has no encryption |

## 7. Split Tunneling vs Full Tunneling

| Mode | Description |
|---|---|
| Full Tunnel | ALL of a client's traffic routes through the VPN | 
| Split Tunnel | Only traffic destined for the private network routes through the VPN; other traffic (e.g., general internet browsing) goes directly out the local connection |

Full tunneling is more secure/controllable (all traffic inspected/protected) but uses more bandwidth on the VPN concentrator; split tunneling is more efficient but expands the attack surface since the device also has a direct, unmonitored path to the internet.

## 8. VPN Authentication

VPNs typically combine **AAA** (see [[AAA]]) with the tunnel: pre-shared keys (PSK), digital certificates (PKI), or username/password (often with MFA) to authenticate the endpoint/user before or during tunnel establishment.

## 9. Security Considerations

- A VPN protects data **in transit** — it does not make the endpoint device itself secure (a compromised client can still exfiltrate data over the tunnel).
- **Split tunneling** can be abused as a pivot point if the client machine is compromised.
- Weak/legacy protocols (PPTP) should never be used for anything sensitive.
- VPN concentrators are high-value targets — must be patched, hardened, and monitored (attackers frequently target VPN appliances for initial access).
- "VPN" as a privacy tool (consumer VPN services) shifts trust from your ISP to the VPN provider — it doesn't grant true anonymity.

## 10. Interview Questions

1. What does a VPN primarily protect? → **Confidentiality and integrity of data in transit**
2. What's the difference between site-to-site and remote access VPN? → **Site-to-site connects two networks; remote access connects a single device to a network**
3. What are the two main IPSec protocols? → **AH (integrity only) and ESP (encryption + integrity)**
4. Which mode encrypts the entire original packet — transport or tunnel? → **Tunnel mode**
5. Why is PPTP considered insecure today? → **Its encryption/authentication (MS-CHAPv2) has known cryptographic weaknesses**
6. What's the trade-off of split tunneling? → **Better performance but a larger, less controlled attack surface**

## 11. Key Points

- VPN = encrypted tunnel across an untrusted network, protecting confidentiality and integrity.
- Two main deployment types: **site-to-site** and **remote access**.
- Key protocols: **IPSec** (AH/ESP, network layer), **SSL/TLS VPN**, **WireGuard** (modern/fast), **OpenVPN**; avoid legacy **PPTP**.
- Tunnel mode encrypts the whole packet; transport mode encrypts payload only.
- Split tunneling trades security for performance; full tunneling is the more locked-down default.

---
*Related: [[NAT]], [[AAA]], [[CIA_Triad]]*
