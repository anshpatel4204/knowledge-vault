## 1. Introduction

Understanding how attackers actually operate — the stages and specific techniques of a real intrusion — is essential for building effective detection and response capability. Two frameworks dominate this space: **Lockheed Martin's Cyber Kill Chain** (a linear, high-level attack lifecycle model) and **MITRE ATT&CK** (a detailed, non-linear knowledge base of real-world adversary tactics and techniques). They're complementary, not competing.

## 2. The Cyber Kill Chain — Seven Stages

Developed by Lockheed Martin, modeling an intrusion as a linear sequence — the core insight being that **breaking the chain at any stage stops the attack**, so defenders don't need to catch every stage, just one.

| Stage | Description | Defensive Focus |
|---|---|---|
| 1. Reconnaissance | Gathering information about the target (OSINT, scanning, social media research) | Limit public exposure, monitor for scanning activity |
| 2. Weaponization | Coupling an exploit with a payload (e.g., a malicious document with an embedded exploit) | Threat intelligence, email/attachment scanning |
| 3. Delivery | Transmitting the weaponized payload to the target (phishing email, infected USB, watering hole) | Email filtering, web filtering, [[Security_Awareness_and_Human_Factor]] |
| 4. Exploitation | Triggering the vulnerability to execute the attacker's code | Patching (see [[Vulnerability_Management]]), application whitelisting |
| 5. Installation | Installing persistent malware/backdoor access on the compromised system | EDR (see [[Endpoint_Security]]), least privilege |
| 6. Command and Control (C2) | Establishing a communication channel back to the attacker for ongoing control | Network monitoring, egress filtering, DNS monitoring |
| 7. Actions on Objectives | The attacker achieves their actual goal — data exfiltration, ransomware deployment, sabotage | DLP (see [[Data_Security_and_Classification]]), network segmentation |

## 3. Limitations of the Cyber Kill Chain

- **Linear assumption** — real attacks often loop back, skip stages, or run multiple stages in parallel, which the strictly sequential model doesn't capture well.
- **External-attacker focus** — designed around traditional perimeter-breach scenarios, less naturally suited to insider threats or cloud-native attack patterns.
- **High-level abstraction** — doesn't specify the actual technical techniques used within each stage, which is exactly the gap MITRE ATT&CK fills.

## 4. MITRE ATT&CK — Overview

A globally accessible, continuously updated **knowledge base** of real-world adversary **Tactics, Techniques, and Procedures (TTPs)**, built from actual observed intrusions rather than a theoretical model — organized as a matrix rather than a strict linear sequence.

| Concept | Definition |
|---|---|
| Tactic | The adversary's **goal** at a given stage (the "why") — e.g., Persistence, Privilege Escalation |
| Technique | The **method** used to achieve that tactic (the "how") — e.g., "Scheduled Task/Job" as a persistence technique |
| Sub-technique | A more specific variant of a technique |
| Procedure | The **specific implementation** a particular threat actor/group actually used |

## 5. MITRE ATT&CK Tactics (Enterprise Matrix)

| Tactic | Goal |
|---|---|
| Reconnaissance | Gathering information to plan future operations |
| Resource Development | Establishing resources to support operations (infrastructure, accounts, capabilities) |
| Initial Access | Gaining an initial foothold in the target environment |
| Execution | Running attacker-controlled code |
| Persistence | Maintaining access across restarts/credential changes (see [[Cron_and_Task_Scheduling]] and [[Systemd_and_Services]] in the Linux folder for concrete persistence mechanisms) |
| Privilege Escalation | Gaining higher-level permissions (see [[Linux_Privilege_Escalation]]) |
| Defense Evasion | Avoiding detection |
| Credential Access | Stealing credentials |
| Discovery | Learning about the environment (network layout, users, systems) |
| Lateral Movement | Moving through the environment to other systems |
| Collection | Gathering data of interest |
| Command and Control | Communicating with compromised systems |
| Exfiltration | Stealing data out of the environment |
| Impact | Manipulating, disrupting, or destroying systems/data (e.g., ransomware deployment) |

## 6. Cyber Kill Chain vs MITRE ATT&CK — Comparison

| Aspect | Cyber Kill Chain | MITRE ATT&CK |
|---|---|---|
| Structure | Linear, 7 fixed stages | Matrix of tactics, non-linear, techniques can apply across stages |
| Level of detail | High-level conceptual model | Highly detailed, specific real-world techniques |
| Source | Developed by Lockheed Martin (theoretical model) | Built from documented real-world intrusions (empirical) |
| Best used for | Communicating the overall attack lifecycle concept, executive-level discussions | Detection engineering, threat hunting, red/blue team exercises, mapping specific defensive coverage |
| Scope | Primarily external attacker perspective | Covers enterprise, mobile, ICS, and cloud-specific matrices |

**In practice:** the Kill Chain is often used to explain the *concept* of "breaking the chain," while MITRE ATT&CK provides the granular, actionable detail security teams actually map their detections and gaps against.

## 7. Using MITRE ATT&CK in Practice

- **Threat intelligence mapping** — describing a specific threat actor's known TTPs in ATT&CK terms for precise, comparable reporting.
- **Detection coverage assessment** — mapping existing SIEM/EDR detection rules against the ATT&CK matrix to visually identify gaps ("we have no detection coverage for Technique T1053 — Scheduled Task/Job").
- **Red team/purple team exercises** — structuring adversary emulation around specific documented techniques rather than ad-hoc testing (see [[Security_Testing_Types]]).
- **Adversary emulation plans** — replicating a specific known threat group's documented behavior to test defenses realistically.

## 8. The Diamond Model (Brief Mention — Related Framework)

A complementary analytical framework describing an intrusion event as four core connected elements: **Adversary, Infrastructure, Capability, Victim** — useful for pivoting between these elements during investigation (e.g., "this infrastructure was also used by what other capability, against what other victims?").

## 9. Interview Questions

1. What's the core defensive insight behind the Cyber Kill Chain model? → **Breaking the attack at any single stage stops the overall intrusion — defenders don't need to catch every stage**
2. What's the difference between a Tactic and a Technique in MITRE ATT&CK? → **A Tactic is the adversary's goal (the "why"); a Technique is the specific method used to achieve it (the "how")**
3. Why is MITRE ATT&CK considered empirical rather than theoretical? → **It's built from documented, real-world observed intrusions rather than being a conceptual model**
4. What's a key limitation of the Cyber Kill Chain compared to MITRE ATT&CK? → **Its strict linear structure doesn't capture how real attacks often loop back, skip stages, or run stages in parallel**
5. How would a security team use MITRE ATT&CK to assess detection gaps? → **By mapping existing detection rules against the matrix to visually identify which specific techniques currently have no coverage**
6. What are the four core elements of the Diamond Model? → **Adversary, Infrastructure, Capability, Victim**

## 10. Key Points

- The **Cyber Kill Chain** is a linear, 7-stage conceptual model — its core value is the insight that breaking any single stage stops the attack.
- **MITRE ATT&CK** is a detailed, empirically-built matrix of real-world **Tactics (why), Techniques (how), and Procedures (specific implementation)**.
- ATT&CK's non-linear, granular structure makes it better suited for **detection engineering, threat hunting, and gap analysis** than the Kill Chain's high-level model.
- Both frameworks are complementary — Kill Chain for conceptual communication, ATT&CK for actionable technical mapping.
- These frameworks directly inform [[Incident_Response]] (understanding attack stage) and [[Security_Testing_Types]] (structuring realistic red team exercises).

---
*Related: [[Incident_Response]], [[Security_Testing_Types]], [[Threat_Actors_and_Hackers]], [[Common_Cyber_Attacks]]*
