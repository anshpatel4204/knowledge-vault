## 1. Introduction

Organizations validate their security posture through several distinct types of testing, each answering a different question and requiring a different methodology. This note is the "capstone" of the Security Basics folder — it ties together vulnerability management, threat actor understanding, and attack frameworks into how security is actually *tested* in practice, and serves as the conceptual bridge into the vault's dedicated VAPT folder.

## 2. Vulnerability Assessment vs Penetration Testing vs Red Teaming

| Aspect | Vulnerability Assessment | Penetration Testing | Red Teaming |
|---|---|---|---|
| Goal | Identify and catalog as many vulnerabilities as possible | Demonstrate real-world exploitability and business impact | Test overall detection/response capability, emulating a real adversary end-to-end |
| Scope | Broad, often automated | Targeted, time-boxed, often a specific system/application | Broad, goal-oriented (e.g., "reach the crown jewel data"), often no defined scope beyond a goal |
| Approach | Largely automated scanning (see [[Vulnerability_Management]]) | Manual exploitation, some automation | Manual, stealthy, multi-technique — mimics real APT-style behavior (see [[Cyber_Kill_Chain_and_MITRE_ATTACK]]) |
| Defenders aware? | Yes | Usually yes | Often no — testing detection/response, not just prevention |
| Output | List of vulnerabilities with severity ratings | Narrative of what was actually exploited and its impact | Assessment of whether the Blue Team detected/responded effectively |

## 3. Types of Penetration Testing (by Knowledge Level)

| Type | Description |
|---|---|
| Black Box | Tester has **no prior knowledge** of internal systems — simulates an external attacker starting from scratch |
| White Box | Tester has **full knowledge** — source code, architecture diagrams, credentials — enables deep, thorough testing but less realistic of an actual external attacker's starting position |
| Gray Box | Tester has **partial knowledge** (e.g., standard user credentials, limited documentation) — balances realism with testing efficiency |

## 4. Types of Penetration Testing (by Target)

| Type | Focus |
|---|---|
| Network Penetration Testing | Internal/external network infrastructure, services, misconfigurations |
| Web Application Penetration Testing | Application-layer vulnerabilities (see [[Web_Application_Attacks_OWASP_Top10]]) |
| Mobile Application Testing | iOS/Android app-specific vulnerabilities |
| Wireless Penetration Testing | Wi-Fi security (see [[Wireless_Networking]] in the Networking folder) |
| Social Engineering Testing | Phishing simulations, pretexting, physical access attempts (see [[Social_Engineering]], [[Physical_Security]]) |
| Physical Penetration Testing | Attempting physical facility access bypass |
| Cloud Penetration Testing | Cloud-specific misconfigurations (IAM, storage buckets, cloud-native services) |

## 5. The Penetration Testing Methodology (High-Level)

```
1. Pre-engagement / Scoping — define rules of engagement, scope, authorization
2. Reconnaissance — passive/active information gathering
3. Scanning & Enumeration — identify live hosts, services, potential entry points
4. Exploitation — attempt to gain access using identified vulnerabilities
5. Post-Exploitation — privilege escalation, lateral movement, data access (within agreed scope)
6. Reporting — document findings, risk ratings, remediation recommendations
7. Remediation Verification — retesting after fixes are applied
```

**Rules of Engagement (RoE):** a formal document defining exactly what's authorized (scope, timing, techniques allowed/prohibited, emergency contacts) — testing without proper authorization, even with good intentions, is illegal (this is precisely the line separating a white-hat pentester from a black-hat attacker — see [[Threat_Actors_and_Hackers]]).

## 6. Red Team vs Blue Team vs Purple Team

| Team | Role |
|---|---|
| Red Team | Offensive — simulates real adversary attacks to test defenses |
| Blue Team | Defensive — detects, responds to, and defends against attacks (SOC analysts, IR teams) |
| Purple Team | Collaborative — Red and Blue teams work together actively, sharing findings in real time to improve detection rather than testing in isolation |

**Why Purple Teaming has grown in popularity:** traditional Red vs Blue exercises can become adversarial/competitive rather than collaborative, and findings may not translate into actual detection improvements if the Blue Team only receives a report after the fact. Purple Teaming closes that loop directly and immediately.

## 7. Bug Bounty Programs

Organizations invite external researchers (a broad, crowd-sourced pool rather than a single contracted firm) to find and responsibly report vulnerabilities in exchange for monetary rewards, scaled by severity. Platforms like HackerOne and Bugcrowd manage these programs at scale. Complements, rather than replaces, formal penetration testing — offers continuous, crowd-sourced coverage between scheduled formal engagements.

## 8. Responsible Disclosure

The practice of privately reporting a discovered vulnerability to the affected vendor/organization, giving them reasonable time to fix it before any public disclosure — balances the security community's need to share knowledge against the risk of publicizing an exploitable flaw before it's patched.

```
Discovery → Private report to vendor → Vendor investigates/patches → 
Coordinated public disclosure (often 30-90 days, negotiated)
```

**Full disclosure** (immediate public release without vendor coordination) and **non-disclosure** (never publishing) are the less common alternatives, each with their own trade-offs and controversy in the security community.

## 9. Security Audits

Distinct from penetration testing — audits assess compliance against a specific standard/framework (see [[Compliance_and_Frameworks]]) rather than attempting active exploitation. An audit might confirm "MFA is configured per policy" without actually attempting to bypass it, whereas a pentest would actively try to bypass it.

## 10. Choosing the Right Testing Type

| Question | Suggests |
|---|---|
| "What vulnerabilities exist across our whole environment?" | Vulnerability Assessment |
| "Can this specific vulnerability actually be exploited, and what's the real impact?" | Penetration Testing |
| "Would our SOC actually detect and respond to a real attack?" | Red Team / Purple Team exercise |
| "Are we compliant with [framework]?" | Security Audit |
| "Do we have ongoing, crowd-sourced vulnerability discovery?" | Bug Bounty Program |

## 11. Interview Questions

1. What's the core difference between a vulnerability assessment and a penetration test? → **Vulnerability assessment identifies and catalogs vulnerabilities (largely automated, broad); penetration testing manually demonstrates real-world exploitability and business impact**
2. What's the difference between black box, white box, and gray box testing? → **Black box: no prior knowledge; white box: full knowledge (source/architecture); gray box: partial knowledge — a balance of realism and depth**
3. Why is Purple Teaming increasingly preferred over traditional isolated Red vs Blue exercises? → **It creates real-time collaboration and feedback between offense and defense, directly improving detection rather than relying on a report delivered after the fact**
4. What must be in place before a legal, authorized penetration test can begin? → **Documented Rules of Engagement defining scope, authorization, timing, and allowed/prohibited techniques**
5. What is responsible disclosure? → **Privately reporting a vulnerability to the vendor, giving them time to patch before any public disclosure**
6. How does a security audit differ from a penetration test in approach? → **An audit assesses compliance against a standard largely through documentation/configuration review; a pentest actively attempts exploitation**

## 12. Key Points

- **Vulnerability assessment** (broad, automated) and **penetration testing** (targeted, manual, impact-focused) answer different questions — see [[Vulnerability_Management]] for the assessment side in depth.
- Pentest knowledge levels: **black box** (none), **white box** (full), **gray box** (partial) — each trading realism against depth/efficiency.
- **Red Team** (offense), **Blue Team** (defense), **Purple Team** (collaborative) — Purple Teaming closes the feedback loop traditional adversarial exercises often miss.
- Legal, authorized testing always requires documented **Rules of Engagement** — this is precisely what separates ethical testing from illegal intrusion.
- **Bug bounty programs** and **responsible disclosure** extend security testing into an ongoing, crowd-sourced, community-driven model beyond scheduled formal engagements.

---
*Related: [[Vulnerability_Management]], [[Cyber_Kill_Chain_and_MITRE_ATTACK]], [[Threat_Actors_and_Hackers]], [[Incident_Response]]*
