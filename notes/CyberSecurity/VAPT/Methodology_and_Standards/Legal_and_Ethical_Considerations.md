## 1. Introduction

Every technique taught in the rest of this VAPT folder is legal in exactly one context: performed against a system you are explicitly, formally authorized to test. This note makes that boundary explicit — it's what separates the "white-hat" pentester from the "black-hat" attacker already introduced in [[Threat_Actors_and_Hackers]], and it's the single most important note in this folder to internalize before touching a tool.

## 2. Relevant Laws (Representative, Not Exhaustive)

| Law / Jurisdiction | What It Criminalizes |
|---|---|
| Computer Fraud and Abuse Act (CFAA) — United States | Unauthorized access to a "protected computer," broadly interpreted |
| Computer Misuse Act — United Kingdom | Unauthorized access to computer material, and unauthorized modification |
| Information Technology Act, 2000 (as amended) — India | Unauthorized access, damage to computer systems, data theft |
| GDPR — European Union | Not a hacking law itself, but governs how any personal data discovered/handled during testing must be treated |

**The unifying principle across jurisdictions:** intent doesn't matter as much as authorization. "I was just trying to help" or "I didn't cause any damage" is not a legal defense against unauthorized access charges.

## 3. What Authorization Actually Looks Like

A signed **Rules of Engagement** document (see [[Scoping_and_Rules_of_Engagement]]), from someone with genuine authority over the systems in scope, is the standard of proof. Verbal permission, an informal email, or permission from someone without actual authority over the asset (e.g., a junior employee "okaying" a test of infrastructure they don't own) does not constitute solid legal protection.

## 4. Ethical Boundaries Beyond Strict Legality

Some actions may be technically within a signed scope but still ethically questionable, and professional pentesters are expected to exercise judgment beyond the letter of the RoE:

| Situation | Professional Expectation |
|---|---|
| Discovering unrelated sensitive data (e.g., another client's data) mid-test | Stop, report immediately, do not access/exfiltrate further than necessary to prove the finding |
| A vulnerability could cause real service disruption if exploited | Confirm with the client before proceeding, even if technically in scope |
| Social engineering targets a specific, named employee | Avoid unnecessarily humiliating or singling out individuals in the final report |
| Test uncovers evidence of an actual ongoing compromise (not the tester's activity) | Immediately escalate — this is a live incident, not a test finding |

## 5. Certifications Reinforcing This Standard

Professional certifications — OSCP, CEH, CompTIA PenTest+ — all require agreeing to a code of ethics as part of certification, reinforcing that the pentesting profession self-regulates around authorized, responsible practice, not just technical skill.

## 6. Interview Questions

1. What single document provides a pentester's legal protection during an engagement? → **A signed Rules of Engagement (RoE) from someone with genuine authority over the in-scope systems**
2. Under laws like the CFAA, does good intent excuse unauthorized access? → **No — authorization is what matters legally, not intent; "trying to help" is not a valid defense**
3. What should a tester do if they discover unrelated sensitive data (e.g., belonging to a different organization) during an authorized test? → **Stop, report it immediately, and avoid accessing or exfiltrating beyond what's necessary to document the finding**
4. If a test uncovers evidence of an actual, ongoing compromise unrelated to the tester's own activity, what should happen? → **Immediate escalation — this is a live security incident, not a test finding, and needs incident response, not a line in the final report**

## 7. Key Points

- **Authorization, not intent, is the legal line** — laws like the CFAA criminalize unauthorized access regardless of good intentions.
- A signed **Rules of Engagement** from someone with real authority is the standard of proof for authorized testing.
- Ethical judgment extends **beyond strict legality** — pentesters are expected to handle unrelated sensitive data, potential disruption, and individual employee dignity responsibly, not just stay technically in-scope.
- Discovering a genuine live incident mid-test requires immediate escalation, not routine reporting.

---
*Related: [[Threat_Actors_and_Hackers]] (CS-Fundamentals/Security Basics), [[Scoping_and_Rules_of_Engagement]], [[Types_of_Pentest_Black_Grey_White_Box]]*
