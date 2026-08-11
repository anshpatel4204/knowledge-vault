## 1. Introduction

**Wireless networking (Wi-Fi)** allows devices to connect to a network without physical cables, using radio frequency signals governed by the **IEEE 802.11** family of standards. It introduces networking concepts (and security challenges) beyond wired Ethernet, since the transmission medium — open air — can't be physically restricted the way a cable can.

## 2. Key Wi-Fi Terminology

| Term | Meaning |
|---|---|
| SSID (Service Set Identifier) | The network's human-readable name (e.g., "HomeWiFi") |
| BSSID | The MAC address of the specific access point broadcasting a network |
| Access Point (AP) | Device that provides wireless connectivity, bridging Wi-Fi clients to a wired network (see [[Network_Devices]]) |
| Channel | A specific frequency sub-band within a Wi-Fi band that an AP transmits on |
| Association | The process of a client connecting to a specific AP |

## 3. 802.11 Standard Generations

| Standard | Marketing Name | Frequency | Max Theoretical Speed | Notes |
|---|---|---|---|---|
| 802.11b | — | 2.4 GHz | 11 Mbps | Legacy |
| 802.11a | — | 5 GHz | 54 Mbps | Legacy |
| 802.11g | — | 2.4 GHz | 54 Mbps | Legacy |
| 802.11n | Wi-Fi 4 | 2.4/5 GHz | 600 Mbps | Introduced MIMO |
| 802.11ac | Wi-Fi 5 | 5 GHz | ~6.9 Gbps | Wider channels, MU-MIMO |
| 802.11ax | Wi-Fi 6 / 6E | 2.4/5/6 GHz | ~9.6 Gbps | OFDMA, better efficiency in dense environments, 6E adds 6 GHz band |
| 802.11be | Wi-Fi 7 | 2.4/5/6 GHz | 40+ Gbps | Latest generation, wider channels, multi-link operation |

## 4. Frequency Bands

| Band | Range | Coverage | Interference | Speed |
|---|---|---|---|---|
| 2.4 GHz | 2.4-2.5 GHz | Longer range, better wall penetration | More crowded (Bluetooth, microwaves, cordless phones) | Slower |
| 5 GHz | 5.1-5.8 GHz | Shorter range | Less crowded | Faster |
| 6 GHz (Wi-Fi 6E/7) | 5.9-7.1 GHz | Shortest range | Least crowded (newest band) | Fastest |

**Channel overlap:** In 2.4 GHz, only channels **1, 6, and 11** are non-overlapping in most regions — using other channels causes interference with neighboring APs, a very common real-world Wi-Fi performance issue.

## 5. Wi-Fi Security Protocols (Evolution)

| Protocol | Encryption | Status |
|---|---|---|
| WEP (Wired Equivalent Privacy) | RC4, static keys | **Completely broken** — crackable in minutes, must never be used |
| WPA (Wi-Fi Protected Access) | TKIP (patched RC4) | Legacy, deprecated, developed as a stopgap after WEP's failure |
| WPA2 | AES-CCMP | Long-standing standard, still widely deployed, secure when using a strong passphrase |
| WPA3 | AES-GCMP, SAE (replaces PSK handshake) | Current standard — resistant to offline dictionary attacks that plagued WPA2 |

### WEP's Fatal Flaw

Used a small, static 24-bit **IV (Initialization Vector)** with RC4, meaning IVs repeated frequently in busy networks — allowing statistical attacks (see [[Cryptographic_Attacks]] in the Cryptography folder) to recover the key from captured traffic within minutes using widely available tools.

### WPA2's Weakness — KRACK

The **Key Reinstallation Attack (KRACK, 2017)** exploited a flaw in WPA2's 4-way handshake, allowing an attacker to force nonce reuse and decrypt traffic — patched via software/firmware updates rather than requiring a new protocol.

### WPA3 Improvements

- **SAE (Simultaneous Authentication of Equals)** replaces WPA2's PSK 4-way handshake, providing resistance to offline dictionary/brute-force attacks even against weak passphrases (an attacker can't just capture a handshake and crack it offline as easily as with WPA2).
- **Forward secrecy** — compromising the passphrase doesn't expose previously captured traffic.
- **192-bit security mode** for enterprise/government use cases.

## 6. Personal vs Enterprise Wi-Fi Security

| Mode | Authentication | Use Case |
|---|---|---|
| WPA2/WPA3-Personal (PSK) | Single shared passphrase for all users | Home networks |
| WPA2/WPA3-Enterprise | 802.1X with a RADIUS server, individual user credentials | Corporate/organizational networks |

**802.1X** (port-based network access control) ties into **AAA** (see [[AAA]] in this folder) — each user authenticates individually against a RADIUS backend rather than sharing one static passphrase, enabling per-user access control, logging, and instant revocation without changing a shared key for everyone.

## 7. Common Wireless Attacks

| Attack | Description |
|---|---|
| Evil Twin | Attacker sets up a rogue AP mimicking a legitimate network's SSID to trick users into connecting, enabling traffic interception |
| Deauthentication Attack | Sending forged 802.11 deauth frames to forcibly disconnect a client, often as a precursor to capturing a fresh WPA handshake for offline cracking, or to push users toward an Evil Twin |
| WPS PIN Brute Force | Wi-Fi Protected Setup's 8-digit PIN has a design flaw that reduces effective brute-force attempts to a fraction of the full keyspace, allowing recovery of the WPA passphrase — WPS should be disabled |
| Rogue Access Point | An unauthorized AP connected to a trusted network, bypassing perimeter security entirely |
| War Driving | Physically searching for and mapping wireless networks and their security posture |

## 8. Wireless Network Hardening Best Practices

- Use **WPA3** (or WPA2 with AES/CCMP, never TKIP/WEP) with a strong, long passphrase.
- Disable **WPS**.
- Use **802.1X/Enterprise** authentication in organizational settings rather than a single shared PSK.
- Segment guest Wi-Fi from internal networks (separate VLAN — see [[VLAN]]).
- Disable SSID broadcast only as a minor deterrent (not real security — SSIDs are still visible in probe requests/responses to anyone actively monitoring).
- Regularly update AP firmware (patches issues like KRACK).

## 9. Interview Questions

1. Why is WEP considered completely broken? → **Its small, reused IVs with RC4 allow statistical key recovery within minutes**
2. What's the main security improvement WPA3 brings over WPA2? → **SAE replaces the PSK handshake, resisting offline dictionary attacks, plus forward secrecy**
3. What's the difference between WPA2-Personal and WPA2-Enterprise? → **Personal uses one shared passphrase (PSK); Enterprise uses 802.1X with individual user authentication via RADIUS**
4. What is an Evil Twin attack? → **A rogue access point mimicking a legitimate SSID to trick users into connecting**
5. Why should WPS be disabled? → **Its PIN design flaw allows brute-forcing the WPA passphrase far faster than the PIN's keyspace would suggest**
6. Which 2.4 GHz channels are non-overlapping? → **1, 6, and 11**

## 10. Key Points

- Wi-Fi is governed by the **802.11** standard family; current generations are **Wi-Fi 6/6E (802.11ax)** and **Wi-Fi 7 (802.11be)**.
- Security evolution: **WEP (broken) → WPA (deprecated) → WPA2 (AES, still common) → WPA3 (current, SAE-based)**.
- **WPA2-Enterprise (802.1X + RADIUS)** provides individual user authentication, unlike the shared-passphrase Personal mode.
- Key attacks: **Evil Twin, deauthentication, WPS brute force, rogue APs** — all specific to the shared, uncontrolled wireless medium.
- Disable **WPS**, use **WPA3/strong WPA2**, and segment guest networks as baseline hardening.

---
*Related: [[Network_Devices]], [[AAA]], [[VLAN]], [[Firewalls]]*
