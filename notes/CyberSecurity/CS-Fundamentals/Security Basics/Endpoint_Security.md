## 1. Introduction

**Endpoint security** protects individual devices — laptops, desktops, servers, mobile devices — that connect to a network, recognizing that endpoints are frequently the initial point of compromise (via malware, phishing, or exploitation) and therefore need dedicated protection beyond what network-perimeter controls alone can provide.

## 2. Why Endpoints Are High-Value Targets

Endpoints are where users actually interact with data and systems — making them the natural target for phishing/social engineering ([[Social_Engineering]]), malware delivery ([[Malware_Types]]), and the typical entry point for broader network compromise. As organizations increasingly rely on remote work and cloud services, endpoints (rather than a hardened network perimeter) have become the primary security boundary in practice.

## 3. Evolution of Endpoint Protection

| Generation | Approach | Limitation |
|---|---|---|
| Traditional Antivirus (AV) | Signature-based detection — matches files against a database of known malware signatures | Ineffective against new/unknown (zero-day) or fileless malware not yet in the signature database |
| Next-Gen Antivirus (NGAV) | Adds heuristics, behavioral analysis, and machine learning to catch novel/unknown threats | Better, but still primarily focused on prevention/blocking |
| EDR (Endpoint Detection and Response) | Continuous monitoring, detection, investigation, and response capabilities — assumes prevention will sometimes fail | Requires more active monitoring/analyst attention to be effective |
| XDR (Extended Detection and Response) | Extends EDR's approach across multiple security layers — endpoint, network, email, cloud — correlating signals for broader visibility | More complex to deploy and tune than single-source EDR |

## 4. How Antivirus Detection Works

| Method | Description |
|---|---|
| Signature-based | Matches a file's hash/pattern against a database of known malware signatures — fast but blind to anything not yet catalogued |
| Heuristic Analysis | Examines code structure/characteristics for suspicious traits resembling known malware patterns, without requiring an exact signature match |
| Behavioral Analysis | Monitors what a program actually *does* at runtime (e.g., attempting to encrypt many files rapidly, modifying system files) rather than what it looks like statically |
| Sandboxing | Executes a suspicious file in an isolated, monitored environment to observe its behavior safely before allowing it on the real system |

## 5. EDR — Endpoint Detection and Response

Goes beyond prevention to provide **visibility and response capability** for threats that get past initial defenses — a critical acknowledgment that no prevention layer is perfect.

**Core EDR capabilities:**
- Continuous endpoint activity monitoring and logging (process execution, network connections, file changes).
- Threat detection via behavioral analytics and threat intelligence correlation.
- Investigation tools allowing analysts to trace an incident's full timeline/scope.
- Response actions — isolating an infected endpoint from the network, killing malicious processes, rolling back changes — often executable remotely without physically touching the device.

## 6. XDR — Extended Detection and Response

Correlates telemetry across **multiple security domains** (endpoint, network, email, cloud, identity) rather than just the endpoint alone — providing broader context that can reveal attack patterns invisible from any single data source. E.g., correlating a phishing email (email security), a suspicious login (identity), and unusual process execution (endpoint) as one connected incident rather than three disconnected alerts.

## 7. Application Control / Allow-listing

Restricts which applications/executables are permitted to run on an endpoint, using either:

| Approach | Description |
|---|---|
| Allow-listing (Whitelisting) | Only explicitly approved applications can run — everything else is blocked by default (most secure, higher administrative overhead) |
| Deny-listing (Blacklisting) | Known-bad applications are blocked; everything else is permitted by default (lower overhead, but reactive — can't block unknown threats) |

Allow-listing is significantly more effective against novel/unknown malware, since it doesn't depend on the malware being previously identified — but requires more disciplined change management to avoid blocking legitimate new software.

## 8. Host-Based Firewalls and HIDS/HIPS

| Control | Function |
|---|---|
| Host-Based Firewall | Filters network traffic at the individual device level (see [[Firewalls]] in Networking folder), independent of network perimeter firewalls |
| HIDS (Host Intrusion Detection System) | Monitors a specific host for signs of compromise (file integrity changes, log anomalies), alerts only |
| HIPS (Host Intrusion Prevention System) | Similar monitoring, but can actively block detected malicious activity in real time |

## 9. Mobile Device Management (MDM) and UEM

| System | Scope |
|---|---|
| MDM (Mobile Device Management) | Manages security policy, configuration, and remote wipe capability specifically for mobile devices |
| UEM (Unified Endpoint Management) | Extends MDM's approach across all endpoint types (mobile, desktop, laptop, IoT) under one unified management platform |

**BYOD (Bring Your Own Device) considerations:** MDM/UEM on personal devices raises privacy questions — solutions like **containerization** (separating work and personal data/apps into isolated profiles on the same device) balance organizational security needs against personal device ownership.

## 10. Endpoint Hardening Baseline

- Disable unnecessary services/features (reduces attack surface — see [[Linux_Security_Hardening]] for the Linux-specific version).
- Enforce full-disk encryption (protects data if the device is lost/stolen — see [[Physical_Security]], [[Data_Security_and_Classification]]).
- Keep OS and applications patched (see [[Vulnerability_Management]]).
- Enforce least privilege — users shouldn't run with local admin/root by default.
- Deploy EDR/NGAV with centralized management and alerting.
- Enforce screen lock/timeout policies.

## 11. Interview Questions

1. Why is signature-based antivirus alone considered insufficient today? → **It can only detect malware matching known signatures already in its database — ineffective against zero-day or fileless malware not yet catalogued**
2. What's the core difference between traditional AV and EDR? → **AV focuses primarily on prevention/blocking; EDR adds continuous monitoring, investigation, and response capability, assuming prevention will sometimes fail**
3. What does XDR add beyond EDR? → **Correlation across multiple security domains (endpoint, network, email, cloud, identity), not just the endpoint alone**
4. What's the difference between application allow-listing and deny-listing? → **Allow-listing permits only explicitly approved applications (secure but higher overhead); deny-listing blocks known-bad applications while permitting everything else by default**
5. What's the difference between HIDS and HIPS? → **HIDS only detects and alerts; HIPS can actively block detected malicious activity in real time**
6. Why does containerization help with BYOD security concerns? → **It separates work and personal data/apps into isolated profiles on the same device, balancing organizational security needs with personal device privacy**

## 12. Key Points

- Endpoint protection has evolved from **signature-based AV → NGAV (behavioral/ML) → EDR (detection + response) → XDR (cross-domain correlation)**.
- **EDR** assumes prevention will sometimes fail and focuses on visibility/response; **XDR** extends that correlation across multiple security data sources.
- **Application allow-listing** is more effective against unknown threats than deny-listing, at the cost of administrative overhead.
- **MDM/UEM** manage endpoint security policy at scale; **containerization** addresses BYOD privacy concerns.
- A solid endpoint hardening baseline (encryption, patching, least privilege, EDR) remains foundational regardless of how sophisticated the surrounding tooling gets.

---
*Related: [[Malware_Types]], [[Vulnerability_Management]], [[Defense_in_Depth_and_Zero_Trust]], [[Incident_Response]]*
