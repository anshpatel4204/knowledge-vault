## How to Use This Folder

This roadmap gives the recommended reading order for the Security Basics folder — from understanding the threat landscape, through defensive philosophy and controls, people/access, governance, infrastructure protection, incident handling, and finally the attacker-behavior frameworks and testing methodologies that tie everything together. Each note links to related ones via `[[wikilinks]]`, including cross-references into the Networking, Cryptography, and Linux folders where concepts overlap.

**Start here:** [[Threat_Actors_and_Hackers]]
**Finish here:** [[Security_Testing_Types]]

## Stage 1 — The Threat Landscape

Know who's attacking and how, before learning how to defend.

1. [[Threat_Actors_and_Hackers]] — script kiddies through APTs and nation-states
2. [[Malware_Types]] — viruses, worms, ransomware, rootkits, botnets
3. [[Social_Engineering]] — phishing, pretexting, tailgating — attacking the human
4. [[Common_Cyber_Attacks]] — DoS/DDoS, MITM, credential attacks, buffer overflow, zero-days
5. [[Web_Application_Attacks_OWASP_Top10]] — SQLi, XSS, CSRF, and the OWASP Top 10

## Stage 2 — Defensive Philosophy & Controls

The frameworks for deciding what to defend and how.

6. [[Security_Controls]] — preventive/detective/corrective, technical/administrative/physical
7. [[Defense_in_Depth_and_Zero_Trust]] — layered defense and "never trust, always verify"
8. [[Risk_Management]] — threat × vulnerability × impact, treatment options
9. [[Vulnerability_Management]] — CVE/CVSS, scanning, patching lifecycle

## Stage 3 — People & Access

The identity layer connecting users to what they're allowed to do.

10. [[Identity_and_Access_Management]] — MFA, SSO/federation, RBAC/ABAC, PAM
11. [[Security_Awareness_and_Human_Factor]] — training, phishing simulations, security culture

## Stage 4 — Governance, Compliance & Data

The organizational and legal structure security operates within.

12. [[Security_Policies_and_Governance]] — policy vs standard vs procedure, roles
13. [[Compliance_and_Frameworks]] — GDPR, HIPAA, PCI-DSS, NIST CSF, ISO 27001, SOC 2
14. [[Data_Security_and_Classification]] — data states, classification, DLP, disposal

## Stage 5 — Protecting Infrastructure

Where digital controls meet the physical and endpoint layers.

15. [[Physical_Security]] — access control, surveillance, environmental controls
16. [[Endpoint_Security]] — AV → EDR → XDR, application control, MDM/UEM

## Stage 6 — When Things Go Wrong

Handling an incident and keeping the business running through it.

17. [[Incident_Response]] — the IR lifecycle, containment, chain of custody
18. [[Business_Continuity_and_Disaster_Recovery]] — RTO/RPO, backup strategy, DR sites

## Stage 7 — Attacker Frameworks & Testing (Capstone)

Ties the whole folder together — how attacks actually unfold, and how defenses get validated.

19. [[Cyber_Kill_Chain_and_MITRE_ATTACK]] — the attack lifecycle and real-world TTP mapping
20. [[Security_Testing_Types]] — vulnerability assessment vs pentest vs red/blue/purple team

## Quick Reference — Full Order

Threat_Actors_and_Hackers → Malware_Types → Social_Engineering → Common_Cyber_Attacks → Web_Application_Attacks_OWASP_Top10 → Security_Controls → Defense_in_Depth_and_Zero_Trust → Risk_Management → Vulnerability_Management → Identity_and_Access_Management → Security_Awareness_and_Human_Factor → Security_Policies_and_Governance → Compliance_and_Frameworks → Data_Security_and_Classification → Physical_Security → Endpoint_Security → Incident_Response → Business_Continuity_and_Disaster_Recovery → Cyber_Kill_Chain_and_MITRE_ATTACK → Security_Testing_Types
