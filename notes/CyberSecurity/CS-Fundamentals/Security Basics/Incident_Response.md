## 1. Introduction

**Incident response (IR)** is the structured, organized approach to handling and managing the aftermath of a security breach or attack — minimizing damage, reducing recovery time/cost, and extracting lessons that improve future defenses. Having a plan **before** an incident occurs is the difference between a controlled, methodical response and reactive chaos.

## 2. What Counts as an "Incident"

A **security event** is any observable occurrence in a system/network (not necessarily bad — a login is an event). A **security incident** is an event that **violates security policy or poses a real threat** — unauthorized access, malware infection, data breach, DoS attack. Not every event escalates to an incident, and triage is exactly the process of telling them apart.

## 3. The Incident Response Lifecycle (NIST SP 800-61 Model)

```
1. Preparation
2. Detection & Analysis
3. Containment, Eradication & Recovery
4. Post-Incident Activity (Lessons Learned)
```

### Stage 1 — Preparation
Everything done **before** an incident occurs to enable an effective response: an IR plan/policy, a trained IR team with clearly defined roles, tooling (SIEM, forensic tools, communication channels), contact lists, and regular tabletop exercises testing the plan against realistic scenarios.

### Stage 2 — Detection and Analysis
Identifying that an incident has actually occurred, then determining its scope, severity, and nature.

- **Detection sources:** SIEM alerts, IDS/IPS, EDR, user reports, threat intelligence feeds, anomaly detection.
- **Analysis:** confirming the incident is real (not a false positive), determining what systems/data are affected, establishing a timeline.
- **Triage/Prioritization:** not all incidents are equal — severity is typically based on impact (data sensitivity, system criticality) and scope (how widespread).

### Stage 3 — Containment, Eradication & Recovery

| Sub-stage | Goal | Examples |
|---|---|---|
| Containment | Stop the incident from spreading/worsening | Isolating affected systems from the network, disabling compromised accounts, blocking malicious IPs |
| Eradication | Remove the root cause entirely | Removing malware, closing the exploited vulnerability, deleting attacker-created accounts/backdoors |
| Recovery | Restore affected systems to normal, verified-clean operation | Restoring from clean backups, rebuilding compromised systems, monitoring closely for recurrence |

**Short-term vs long-term containment:** short-term containment stops immediate bleeding (e.g., pulling a network cable); long-term containment applies a more durable fix while the system remains operational (e.g., temporary firewall rules) until full eradication is possible.

### Stage 4 — Post-Incident Activity (Lessons Learned)
A **blameless post-mortem** reviewing what happened, how effectively the response worked, and what should change — feeding directly back into updated policies, controls, and training. Skipping this stage is one of the most common real-world IR program failures, wasting the most valuable output of a costly incident.

## 4. Incident Response Team Roles

| Role | Responsibility |
|---|---|
| Incident Commander/Manager | Coordinates the overall response, makes key decisions |
| Security Analysts | Perform technical investigation, containment, eradication |
| Forensic Investigators | Preserve and analyze evidence (chain of custody matters if legal action may follow — see the DF folder) |
| Communications/PR | Manages internal and external communications, including regulatory notifications |
| Legal Counsel | Advises on breach notification obligations, liability, evidence handling |
| Management/Executive Sponsor | Provides authority and resources, makes business-impact decisions |

## 5. Chain of Custody

When evidence might support legal action or regulatory reporting, its handling must be meticulously documented — who collected it, when, how it was stored, and who accessed it afterward — to preserve its integrity and admissibility. A broken chain of custody can render otherwise-solid evidence unusable.

## 6. Communication During an Incident

| Audience | Consideration |
|---|---|
| Internal stakeholders | Need timely, accurate status without causing panic or leaking sensitive details broadly |
| Customers/Public | Governed by breach notification laws (see [[Compliance_and_Frameworks]] — GDPR's 72-hour rule, HIPAA breach notification) |
| Regulators | Specific, often legally mandated notification timelines and content requirements |
| Law Enforcement | May be required or advisable depending on the nature/severity of the incident |

**Out-of-band communication:** if the attacker may have access to normal communication channels (email, chat), the IR team should use a separate, presumed-uncompromised channel to coordinate the response without tipping off the attacker.

## 7. Incident Classification/Severity

Organizations typically define severity tiers (e.g., SEV1-SEV4) mapping to response urgency and escalation requirements — driven by factors like data sensitivity, system criticality, and scope of impact, directly connecting to the [[Risk_Management]] concepts of likelihood and impact.

## 8. Tabletop Exercises

Structured, discussion-based simulations walking a team through a hypothetical incident scenario **without actually executing technical actions** — surfaces gaps in the plan, unclear roles, and communication breakdowns in a low-stakes setting before a real incident forces the team to learn those lessons live.

## 9. IR and the Cyber Kill Chain / MITRE ATT&CK

Understanding attacker methodology (see [[Cyber_Kill_Chain_and_MITRE_ATTACK]]) directly informs IR — knowing which stage of an attack you're observing (initial access vs lateral movement vs exfiltration) shapes both urgency and the appropriate containment strategy.

## 10. Common IR Mistakes

| Mistake | Consequence |
|---|---|
| No documented plan | Chaotic, inconsistent response, wasted time during a high-pressure event |
| Containing before fully understanding scope | May tip off the attacker prematurely or miss other affected systems |
| Skipping the lessons-learned stage | Repeating the same preventable mistakes in future incidents |
| Poor communication | Panic, misinformation, or regulatory/legal exposure from missed notification deadlines |
| Contaminating evidence | Compromises forensic value and potential legal action |
| Not testing the plan | An untested plan often fails in ways only discovered during an actual crisis |

## 11. Interview Questions

1. What are the four main stages of the NIST incident response lifecycle? → **Preparation, Detection & Analysis, Containment/Eradication/Recovery, Post-Incident Activity**
2. What's the difference between a security event and a security incident? → **An event is any observable occurrence; an incident is an event that violates policy or poses a real threat**
3. What's the difference between short-term and long-term containment? → **Short-term stops immediate spread quickly (e.g., disconnecting a system); long-term applies a more durable fix while keeping systems operational until full eradication**
4. Why is chain of custody important during incident response? → **It preserves evidence integrity and admissibility if legal action or regulatory reporting follows**
5. Why might an IR team use out-of-band communication during a response? → **If the attacker may have access to normal channels (email/chat), a separate channel avoids tipping them off**
6. What's the value of a tabletop exercise? → **It surfaces plan gaps, unclear roles, and communication issues in a low-stakes simulation before a real incident forces those lessons to be learned live**

## 12. Key Points

- IR follows a structured lifecycle: **Preparation → Detection & Analysis → Containment/Eradication/Recovery → Post-Incident Activity**.
- **Preparation** (plan, trained team, tooling, tabletop exercises) determines how well an organization handles the inevitable.
- **Containment** should balance stopping the spread against preserving evidence/visibility into the attacker's full scope.
- **Chain of custody** and careful communication (including regulatory notification deadlines) carry real legal weight.
- The **lessons-learned stage** is frequently skipped but is the step that actually improves future resilience — an incident without a post-mortem wastes its most valuable output.

---
*Related: [[Cyber_Kill_Chain_and_MITRE_ATTACK]], [[Compliance_and_Frameworks]], [[Risk_Management]], [[Business_Continuity_and_Disaster_Recovery]]*
