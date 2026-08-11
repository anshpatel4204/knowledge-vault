## 1. Introduction

**Defense in Depth** and **Zero Trust** are two foundational security architecture philosophies describing *how* controls should be structured across an environment. Defense in Depth is about layering redundant defenses; Zero Trust is about eliminating implicit trust entirely, regardless of location. They're complementary, not competing, and modern security architecture typically applies both together.

## 2. Defense in Depth — Core Concept

The principle of layering **multiple, independent security controls** so that the failure of any single control doesn't result in total compromise — analogous to a castle's layered defenses (moat, walls, guards, inner keep) rather than relying on one strong gate alone.

```
Attacker → Perimeter Firewall → Network Segmentation → Host Firewall →
           Endpoint Protection → Application Controls → Data Encryption
           (each layer must be defeated independently)
```

## 3. Defense in Depth Layers

| Layer | Example Controls |
|---|---|
| Perimeter | Firewalls, DDoS protection (see [[Firewalls]] in Networking folder) |
| Network | Segmentation, VLANs, IDS/IPS (see [[VLAN]]) |
| Host | Endpoint protection, host firewalls, patch management (see [[Endpoint_Security]]) |
| Application | Input validation, secure coding, WAF (see [[Web_Application_Attacks_OWASP_Top10]]) |
| Data | Encryption at rest/in transit, DLP, access control (see [[Data_Security_and_Classification]]) |
| Physical | Locks, badges, cameras (see [[Physical_Security]]) |
| Human | Security awareness training (see [[Security_Awareness_and_Human_Factor]]) |

**Key principle:** these layers should be **independent** — a flaw in one shouldn't automatically compromise the next. A firewall failing shouldn't also mean data is unencrypted; a phished credential shouldn't also grant unrestricted access if least privilege and MFA are properly layered on top.

## 4. Least Privilege

Every user, process, and system should have **only the minimum access necessary** to perform its function — a foundational principle underlying nearly every other control (see [[Identity_and_Access_Management]], [[Security_Controls]]). Reduces both the attack surface and the blast radius of any single compromise.

## 5. Network Segmentation

Dividing a network into smaller, isolated zones so that compromise of one segment doesn't automatically grant access to others — implemented via VLANs, firewalls between zones, or micro-segmentation in cloud/virtualized environments. Directly limits lateral movement, a critical phase in most real-world breaches (see [[Cyber_Kill_Chain_and_MITRE_ATTACK]]).

## 6. Zero Trust — Core Concept

**"Never trust, always verify."** Zero Trust rejects the traditional model of a trusted internal network protected by a hardened perimeter ("castle-and-moat") — instead, **every** access request is verified based on identity, device health, and context, regardless of whether it originates inside or outside the traditional network boundary.

**Why the shift happened:** the castle-and-moat model assumes anything inside the perimeter is trustworthy — but cloud adoption, remote work, mobile devices, and the reality that attackers routinely do breach the perimeter (via phishing, VPN compromise, insider threats) made that assumption dangerously outdated.

## 7. Zero Trust Core Principles

| Principle | Description |
|---|---|
| Verify explicitly | Always authenticate and authorize based on all available signals — identity, device, location, workload — not just network location |
| Least privilege access | Limit access with just-in-time and just-enough-access (JIT/JEA), minimizing standing privilege |
| Assume breach | Design as if an attacker is already inside — minimize blast radius, segment access, verify end-to-end encryption, use analytics to detect anomalies |

## 8. Zero Trust Architecture Components

| Component | Role |
|---|---|
| Identity Provider (IdP) | Centralized, strongly authenticated identity as the new security perimeter (see [[Identity_and_Access_Management]]) |
| Policy Engine | Evaluates each access request against policy (identity, device posture, context) in real time |
| Micro-segmentation | Granular network isolation, often down to the individual workload/application level, far finer-grained than traditional VLANs |
| Continuous Monitoring | Ongoing verification rather than a one-time login check — sessions can be re-evaluated or revoked mid-session based on changing risk signals |
| Device Trust/Posture Assessment | Verifying the requesting device meets security requirements (patched, encrypted, not jailbroken) before granting access |

## 9. Defense in Depth vs Zero Trust — How They Relate

| Aspect | Defense in Depth | Zero Trust |
|---|---|---|
| Core idea | Layer multiple independent controls | Eliminate implicit trust based on network location |
| Trust model | Often still assumes some level of internal trust once past the perimeter | No implicit trust anywhere — verify every request |
| Primary goal | Redundancy — no single point of failure | Continuous verification — no assumed safety zone |
| Relationship | Zero Trust can be understood as one modern expression/evolution of defense in depth applied specifically to trust and access decisions | Complements defense in depth rather than replacing its other layers (data, application, physical still matter) |

Many organizations implement Zero Trust **as part of** a broader defense-in-depth strategy — they're not mutually exclusive, and a mature architecture uses both.

## 10. Practical Zero Trust Example

A traditional VPN grants broad network access once connected. A Zero Trust approach instead grants access **per-application**, continuously verifying the user's identity, device posture, and context for each specific resource requested — so a compromised device can't simply pivot freely across the internal network the way it could with traditional VPN-granted broad access.

## 11. Interview Questions

1. What is the core idea behind Defense in Depth? → **Layering multiple independent security controls so no single point of failure results in total compromise**
2. What does "never trust, always verify" mean in Zero Trust? → **Every access request is authenticated and authorized based on identity/device/context, regardless of whether it originates inside or outside the traditional network perimeter**
3. Why did the traditional "castle-and-moat" security model become inadequate? → **Cloud adoption, remote work, and the reality that attackers routinely breach perimeters made the assumption that "inside = trusted" dangerously outdated**
4. How does network segmentation support defense in depth? → **It limits lateral movement, so compromising one segment doesn't automatically grant access to the rest of the network**
5. What's the relationship between Zero Trust and Defense in Depth? → **Zero Trust is a modern evolution focused specifically on eliminating implicit trust/access assumptions; it complements, rather than replaces, the other layers of defense in depth (data, application, physical)**
6. What does "assume breach" mean as a Zero Trust design principle? → **Architecting systems as if an attacker is already inside, minimizing blast radius and relying on continuous detection rather than perimeter defense alone**

## 12. Key Points

- **Defense in Depth** layers independent controls across perimeter, network, host, application, data, physical, and human layers.
- **Zero Trust** eliminates implicit trust based on network location — "never trust, always verify," applied continuously, not just at login.
- **Least privilege** and **network segmentation/micro-segmentation** are shared foundational tools for both philosophies.
- Zero Trust is best understood as a **modern evolution of trust/access decisions within** a broader defense-in-depth strategy, not a replacement for it.
- Both philosophies assume failure is possible somewhere — the goal is limiting blast radius, not achieving an impossible perfect perimeter.

---
*Related: [[Security_Controls]], [[Identity_and_Access_Management]], [[Risk_Management]]*
