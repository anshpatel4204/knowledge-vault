## 1. Introduction

This note catalogs the core categories of technical cyber attacks that don't fit specifically into malware ([[Malware_Types]]), social engineering ([[Social_Engineering]]), or web-application-specific attacks ([[Web_Application_Attacks_OWASP_Top10]]) — the general-purpose attack techniques referenced constantly across networking, application, and system security.

## 2. Denial of Service (DoS) and Distributed DoS (DDoS)

Overwhelms a target's resources (bandwidth, CPU, memory, application capacity) so legitimate users can't access the service.

| Type | Description |
|---|---|
| Volumetric | Floods network bandwidth with traffic (e.g., UDP floods, DNS/NTP amplification — see [[DNS]] and [[ICMP]] in the Networking folder) |
| Protocol | Exploits weaknesses in protocol handling (e.g., SYN flood exhausting a server's connection queue) |
| Application-Layer | Targets application logic/resources with seemingly legitimate requests (e.g., HTTP flood, Slowloris keeping connections open) |

**DoS vs DDoS:** DoS originates from a single source; DDoS is distributed across many sources (often a botnet — see [[Malware_Types]]), making it far harder to block by simply blacklisting one IP.

**Mitigation:** rate limiting, SYN cookies, CDN/DDoS scrubbing services, traffic anomaly detection, over-provisioned capacity.

## 3. Man-in-the-Middle (MITM)

An attacker secretly intercepts (and potentially alters) communication between two parties who believe they're communicating directly. Covered in depth in the Networking folder under [[ARP]] (ARP spoofing) and touched in [[Wireless_Networking]] (Evil Twin). Mitigated primarily by encryption and authentication — TLS certificate validation (see [[TLS_SSL]] in the Cryptography folder) is the standard defense.

## 4. Brute Force and Credential Attacks

| Attack | Description |
|---|---|
| Brute Force | Systematically trying every possible password/key combination |
| Dictionary Attack | Trying a curated list of common/likely passwords rather than the full keyspace |
| Credential Stuffing | Using username/password pairs leaked from one breach to attempt logins on other, unrelated services — effective because of widespread password reuse |
| Password Spraying | Trying one or a few common passwords across MANY accounts, avoiding per-account lockout thresholds that would trigger from repeated attempts against a single account |

**Mitigation:** account lockout/rate limiting, MFA (see [[Identity_and_Access_Management]]), strong unique passwords, monitoring for anomalous login patterns (impossible travel, unusual volume).

## 5. Session Hijacking

Stealing or predicting a valid session token/cookie to impersonate an authenticated user without needing their actual credentials.

| Technique | Description |
|---|---|
| Session Sidejacking | Sniffing unencrypted session cookies over an insecure network |
| Cross-Site Scripting (XSS)-based theft | Using injected script to steal session cookies (see [[Web_Application_Attacks_OWASP_Top10]]) |
| Session Fixation | Tricking a victim into using a session ID the attacker already knows, then hijacking it once the victim authenticates |
| Predictable Session Tokens | Weak/predictable session ID generation allows guessing valid sessions (ties to [[Random_Number_Generation]] in the Cryptography folder) |

## 6. Buffer Overflow

Occurs when a program writes more data to a buffer than it was allocated to hold, potentially overwriting adjacent memory — historically one of the most impactful classes of vulnerabilities, allowing attackers to crash a program or, in the worst case, execute arbitrary injected code.

**Modern mitigations:** ASLR (Address Space Layout Randomization), stack canaries, DEP/NX bit (non-executable memory regions), and memory-safe languages (Rust, Go) that structurally prevent this class of bug.

## 7. Zero-Day Attacks

Exploits a vulnerability that is **unknown to the vendor** (and therefore unpatched) at the time of the attack — "zero days" refers to the vendor having had zero days to fix it before exploitation began.

**Lifecycle:** vulnerability discovered → (responsibly disclosed to vendor OR sold/exploited maliciously) → patch developed → patch released → the vulnerability is no longer "zero-day" once a fix exists, though it remains dangerous until widely patched ("n-day" exploitation of unpatched-but-known vulnerabilities is extremely common in practice).

**Why they're valuable:** zero-days bypass signature-based defenses entirely since no detection rule yet exists — they're highly prized by both criminal markets and nation-state actors (see [[Threat_Actors_and_Hackers]]).

## 8. Privilege Escalation

Gaining higher-level access than initially granted. Covered in exhaustive technical/practical detail in [[Linux_Privilege_Escalation]] (Linux folder) — conceptually applies equally to Windows and cloud environments via analogous misconfigurations (weak service permissions, token abuse, IAM policy misconfigurations).

| Type | Description |
|---|---|
| Vertical | Gaining higher privilege than currently held (user → admin/root) |
| Horizontal | Gaining access to a different account at the same privilege level (accessing another user's data) |

## 9. Man-in-the-Browser (MITB)

A more specific variant of MITM where malware embeds itself directly in the victim's browser (via a malicious extension or injected code), able to modify web pages, transactions, or form data in real time — even over an otherwise properly encrypted HTTPS connection, since the compromise happens after decryption on the client side.

## 10. Watering Hole Attacks

Attackers compromise a website known to be frequently visited by a specific target group (e.g., an industry-specific forum), infecting it with malware to compromise visitors — an indirect approach when directly attacking the target organization is too difficult.

## 11. Supply Chain Attacks

Compromising a trusted third-party vendor, software dependency, or update mechanism to indirectly reach the actual target — increasingly common and severe because it exploits inherent trust relationships. Notable examples: **SolarWinds** (compromised software update mechanism), **npm/PyPI package compromises** (malicious open-source dependencies).

## 12. DNS-Based Attacks (Cross-Reference)

Covered fully in [[DNS]] in the Networking folder: DNS spoofing/cache poisoning, DNS tunneling, DNS amplification DDoS.

## 13. Attack Categories Comparison

| Attack | Primary Goal | Primary Defense |
|---|---|---|
| DoS/DDoS | Availability disruption | Rate limiting, scrubbing services, redundancy |
| MITM | Confidentiality/integrity compromise | Encryption (TLS), certificate validation |
| Credential Stuffing | Unauthorized account access | MFA, unique passwords, breach monitoring |
| Session Hijacking | Impersonating an authenticated user | Secure/HttpOnly cookies, TLS, short session lifetimes |
| Buffer Overflow | Code execution/crash | ASLR, DEP, memory-safe languages, input validation |
| Zero-Day | Undetected exploitation | Defense in depth, EDR, rapid patching once disclosed |
| Supply Chain | Indirect compromise via trusted third party | Vendor risk management, SBOM, code signing verification |

## 14. Interview Questions

1. What's the difference between DoS and DDoS? → **DoS originates from a single source; DDoS is distributed across many sources, often a botnet**
2. What's the difference between credential stuffing and password spraying? → **Credential stuffing reuses leaked username/password pairs across services; password spraying tries a few common passwords across many accounts to avoid lockout thresholds**
3. Why is a zero-day exploit so valuable to an attacker? → **It targets a vulnerability unknown to the vendor, so no patch or signature-based detection exists yet**
4. What's the difference between vertical and horizontal privilege escalation? → **Vertical gains higher privilege (user to admin); horizontal gains access to a different account at the same privilege level**
5. What is a supply chain attack, and why is it especially dangerous? → **Compromising a trusted third-party vendor/dependency to indirectly reach the real target — dangerous because it exploits inherent trust and can affect many downstream victims at once**
6. What memory-safety mitigations help prevent buffer overflow exploitation? → **ASLR, stack canaries, DEP/NX bit, and memory-safe programming languages**

## 15. Key Points

- **DoS/DDoS** target availability; **MITM** targets confidentiality/integrity of communications in transit.
- **Credential stuffing** and **password spraying** exploit password reuse and lockout-threshold gaps respectively — MFA is the strongest single mitigation.
- **Buffer overflows** remain historically significant, mitigated today by ASLR/DEP/stack canaries and memory-safe languages.
- **Zero-day** exploits target unknown/unpatched vulnerabilities, making them especially valuable and hard to defend against directly.
- **Supply chain attacks** (SolarWinds-style) are an increasingly critical, high-impact category exploiting third-party trust relationships.

---
*Related: [[Malware_Types]], [[Web_Application_Attacks_OWASP_Top10]], [[Vulnerability_Management]], [[Identity_and_Access_Management]]*
