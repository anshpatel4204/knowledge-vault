## 1. Introduction

A **security control** is any safeguard or countermeasure implemented to reduce risk to an organization's assets. Controls are classified along two independent dimensions — **by function** (what they do) and **by type/category** (how they're implemented) — and a mature security program deliberately combines multiple categories rather than relying on any single control.

## 2. Controls by Function

| Function | Purpose | Examples |
|---|---|---|
| Preventive | Stop an incident before it occurs | Firewalls, access control, encryption, security awareness training |
| Detective | Identify an incident that is occurring or has occurred | IDS, SIEM alerts, log monitoring, audit trails |
| Corrective | Limit the extent of damage and restore normal operations after an incident | Backups/restoration, patching, incident response procedures |
| Deterrent | Discourage an attack from being attempted at all | Warning banners, visible security cameras, legal notices |
| Compensating | An alternative safeguard used when the primary control can't be implemented, providing equivalent protection | Additional monitoring on a system that can't be patched immediately (e.g., legacy system awaiting decommission) |
| Directive | Direct or mandate a required action/behavior | Security policies, mandated procedures, required training |

## 3. Controls by Type/Category

| Category | Description | Examples |
|---|---|---|
| Technical (Logical) | Implemented via technology | Firewalls, encryption, access control lists, MFA, antivirus |
| Administrative (Managerial) | Policies, procedures, and processes governing behavior | Security policies, background checks, security training, risk assessments |
| Physical | Controls protecting the physical environment | Locks, badges, security guards, CCTV, environmental controls (see [[Physical_Security]]) |

## 4. The Full Control Matrix

Combining function and category produces a fuller picture — e.g., a firewall is a **technical + preventive** control; a security guard is a **physical + preventive** control; an audit log review is a **technical + detective** control; a background check policy is **administrative + preventive**.

| | Preventive | Detective | Corrective |
|---|---|---|---|
| Technical | Firewall, MFA, encryption | IDS, SIEM, file integrity monitoring | Automated patching, backup restoration |
| Administrative | Security policy, training, separation of duties | Security audits, mandatory log reviews | Incident response plan execution |
| Physical | Locks, fences, badge access | CCTV, motion sensors, security guards on patrol | Fire suppression systems, facility repair |

## 5. Deterrent vs Preventive — A Common Distinction

A **deterrent** control discourages an attack from being attempted (a visible camera might make an attacker choose a different target); a **preventive** control actually stops the attack even if attempted (a locked door physically blocks entry regardless of intent). Many controls serve both roles simultaneously, but conceptually they answer different questions — "will they try?" vs "can they succeed if they try?"

## 6. Compensating Controls in Practice

Used when the ideal/primary control isn't feasible — a very common real-world scenario, especially relevant in compliance contexts (e.g., PCI-DSS explicitly allows documented compensating controls when a specific requirement can't be met directly).

**Example:** a legacy medical device running an unpatchable, end-of-life OS can't receive security updates — a compensating control might be strict network segmentation isolating it entirely from other systems, combined with enhanced monitoring on that segment.

## 7. Separation of Duties and Dual Control

Administrative controls preventing any single individual from having enough unilateral access/authority to compromise a critical process alone.

| Concept | Description |
|---|---|
| Separation of Duties | Splitting a sensitive process across multiple people (e.g., the person who requests a payment isn't the same person who approves it) |
| Dual Control | Requiring two authorized individuals to jointly perform a highly sensitive action (e.g., two people needed to access a bank vault, or [[Key_Management]]'s split-knowledge concept in the Cryptography folder) |
| Least Privilege | Each person/process gets only the minimum access needed for their role (see [[Identity_and_Access_Management]]) |
| Job Rotation | Periodically moving people between roles, making sustained fraud harder to conceal |
| Mandatory Vacation | Forcing employees to take leave, during which someone else covers their duties — often surfaces fraud that depended on the original person's continuous presence |

## 8. Selecting Controls — Cost vs Risk

Controls should be selected based on the [[Risk_Management]] process — a control costing more than the risk it mitigates isn't justified. This is why risk assessment logically precedes control selection in a mature security program, rather than implementing controls reflexively.

## 9. Defense in Depth (Brief — see [[Defense_in_Depth_and_Zero_Trust]] for full detail)

No single control category is sufficient alone — layering preventive, detective, and corrective controls across technical, administrative, and physical categories ensures that the failure of any one control doesn't result in total compromise.

## 10. Interview Questions

1. What's the difference between a preventive and a detective control? → **Preventive stops an incident before it happens; detective identifies one that is happening or already happened**
2. Give an example of a compensating control and when it would be used. → **Enhanced network segmentation/monitoring around a legacy system that can't be patched — used when the ideal primary control isn't feasible**
3. What are the three main control categories (by type)? → **Technical, administrative, physical**
4. What's the difference between separation of duties and least privilege? → **Separation of duties splits a sensitive process across multiple people; least privilege limits any one person/process to only the access their role requires**
5. Why is mandatory vacation considered a security control? → **It forces coverage by someone else, often surfacing fraud or irregularities that depended on the original person's continuous, unchecked presence**
6. Classify a security awareness training program by function and category. → **Administrative + Preventive (also arguably Directive)**

## 11. Key Points

- Controls are classified by **function** (preventive, detective, corrective, deterrent, compensating, directive) and by **category** (technical, administrative, physical) — two independent dimensions.
- A mature program layers controls across **both dimensions** rather than relying on any single type.
- **Compensating controls** provide equivalent protection when the ideal primary control can't be implemented.
- **Separation of duties, dual control, least privilege, and mandatory vacation** are key administrative controls limiting the damage any single compromised or malicious individual can cause.
- Control selection should be driven by [[Risk_Management]] — cost should be proportionate to the risk being mitigated.

---
*Related: [[Risk_Management]], [[Defense_in_Depth_and_Zero_Trust]], [[Identity_and_Access_Management]], [[Physical_Security]]*
