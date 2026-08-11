## 1. Introduction

**Physical security** protects people, facilities, hardware, and — by extension — the data and systems they house, from physical threats: unauthorized access, theft, vandalism, and environmental hazards. It's easy to overlook in a field dominated by digital threats, but no amount of network/application security matters if an attacker can simply walk in and steal a server or plug a malicious device directly into the network.

## 2. Why Physical Security Matters in Cybersecurity

Physical access frequently defeats logical/technical controls entirely — an attacker with physical access to a device can potentially: boot from external media to bypass OS-level authentication (see [[Linux_Boot_Process]] in the Linux folder), extract a hard drive to read data directly, install a hardware keylogger, or plug into a network port to gain internal network access without ever touching a firewall. This is exactly why physical security is one of the three core control categories alongside technical and administrative controls (see [[Security_Controls]]).

## 3. Layered Physical Security (Defense in Depth Applied Physically)

```
Perimeter → Building Access → Floor/Room Access → Equipment/Rack Access → Device-Level Controls
```

| Layer | Example Controls |
|---|---|
| Perimeter | Fencing, gates, lighting, signage, perimeter cameras |
| Building Entry | Reception/security guards, badge readers, visitor logs |
| Interior Zones | Mantraps, badge-restricted floors/rooms, biometric access |
| Server Rooms/Data Centers | Cage/rack locks, enhanced access logging, environmental monitoring |
| Device-Level | Cable locks, asset tags, tamper-evident seals, drive encryption as a last line of defense |

## 4. Access Control Mechanisms

| Mechanism | Description |
|---|---|
| Badge/Card Readers | Electronic access control tied to individual identity, logs entry/exit |
| Biometric Access | Fingerprint, iris, facial recognition — hard to share/steal compared to a badge, but raises its own privacy considerations |
| PIN/Keypad | Simple but shareable — often combined with another factor for higher-security areas |
| Mantrap (Access Control Vestibule) | A small interlocking double-door space allowing only one verified person through at a time, specifically preventing tailgating |
| Turnstiles | Physical barriers enforcing one-person-at-a-time entry, often paired with badge readers |

## 5. Tailgating and Piggybacking (Cross-Reference)

Covered in [[Social_Engineering]] — following an authorized person through a secured entry point without individually presenting credentials. Physical controls like mantraps and a security-aware culture (challenging unfamiliar individuals without visible badges) are the primary defenses.

## 6. Surveillance and Monitoring

| Control | Purpose |
|---|---|
| CCTV | Both a **detective** control (recording activity for later review/investigation) and a **deterrent** (visible cameras discourage attempts) — see [[Security_Controls]] |
| Security Guards | Provide both active monitoring and a human deterrent/response capability |
| Motion Sensors | Detect movement in restricted/after-hours areas |
| Alarm Systems | Alert on unauthorized entry attempts or breaches |
| Visitor Logs | Track who entered, when, and who they were visiting — accountability and audit trail |

## 7. Environmental Controls

Protecting equipment from environmental hazards that could cause data loss or service disruption, distinct from deliberate intrusion but equally part of physical security's scope.

| Control | Protects Against |
|---|---|
| HVAC (climate control) | Overheating, humidity damage to equipment |
| Fire Suppression | Fire damage — specialized systems (e.g., clean agent suppression) used in data centers to avoid water damage to electronics that traditional sprinklers would cause |
| Uninterruptible Power Supply (UPS) | Short-term power loss, power fluctuations |
| Backup Generators | Extended power outages |
| Water/Flood Detection | Water damage from leaks, flooding |
| Redundant/Diverse Power and Network Paths | Single points of failure in utility service |

## 8. Asset Management and Physical Inventory

Tracking the physical location, custody, and lifecycle of hardware assets — laptops, servers, mobile devices, removable media — supports both security (knowing what exists and where) and incident response (quickly identifying what's affected in a physical theft or loss scenario). Ties into the broader asset inventory concept covered in [[Vulnerability_Management]].

## 9. Media Handling and Disposal

Physical media (hard drives, USB drives, backup tapes, printed documents) requires secure handling throughout its lifecycle and secure disposal at end-of-life — see [[Data_Security_and_Classification]] for the specific disposal methods (degaussing, physical destruction, cryptographic erasure).

## 10. Social Engineering's Physical Dimension (Cross-Reference)

Several social engineering techniques are inherently physical — **pretexting** to gain building access, **shoulder surfing** to observe credential entry, **dumpster diving** for discarded sensitive material (see [[Social_Engineering]]). Physical security policy (clean desk policy, shredding requirements, visitor escort rules) directly mitigates these.

## 11. Physical Security for Remote/Hybrid Work

Extends beyond the traditional office perimeter — device encryption (protecting data if a laptop is lost/stolen outside the office), privacy screens (against shoulder surfing in public spaces), secure home Wi-Fi configuration, and policies around discussing sensitive information in public.

## 12. Interview Questions

1. Why does physical access often defeat purely logical/technical security controls? → **Physical access enables bypassing OS authentication (booting from external media), direct drive extraction, hardware keyloggers, or direct network port access — circumventing controls that assume the device/network boundary is secure**
2. What is a mantrap, and what does it specifically prevent? → **A double-door interlocking vestibule allowing only one verified person through at a time — specifically prevents tailgating**
3. Is CCTV a preventive or detective control? → **Both — detective in that it records activity for review, and it also serves a deterrent function by being visible**
4. Why do data centers typically use clean agent fire suppression instead of traditional water sprinklers? → **Water sprinklers would cause significant additional damage to electronic equipment; clean agent systems suppress fire without that side effect**
5. What physical security control specifically defends against a lost or stolen laptop's data being exposed? → **Full-disk encryption**
6. How does physical security connect to social engineering? → **Techniques like tailgating, shoulder surfing, and dumpster diving are inherently physical, and are mitigated by physical controls and policy (mantraps, clean desk policy, shredding)**

## 13. Key Points

- Physical security is layered (**perimeter → building → interior zones → equipment**), mirroring defense in depth applied to the physical world.
- Access control mechanisms (**badges, biometrics, mantraps**) specifically address unauthorized entry and tailgating.
- **Environmental controls** (HVAC, fire suppression, UPS, generators) protect against non-malicious but equally damaging disruptions.
- Physical access frequently **bypasses logical controls entirely** — it's a foundational, easily underestimated layer of a complete security program.
- Physical security and [[Social_Engineering]] overlap significantly — tailgating, shoulder surfing, and dumpster diving are physical-world attack techniques with physical-world defenses.

---
*Related: [[Security_Controls]], [[Social_Engineering]], [[Data_Security_and_Classification]], [[Business_Continuity_and_Disaster_Recovery]]*
