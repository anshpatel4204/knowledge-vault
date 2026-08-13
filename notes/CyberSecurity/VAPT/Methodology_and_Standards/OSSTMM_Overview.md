## 1. Introduction

The **Open Source Security Testing Methodology Manual (OSSTMM)**, maintained by ISECOM, takes a different angle from PTES: instead of a phase checklist, it emphasizes **measurable, fact-based testing** — producing a quantifiable score of an organization's actual security posture (called the **RAV — Risk Assessment Value**) rather than a narrative list of findings alone.

## 2. Core Philosophy

OSSTMM's guiding principle is that security should be tested the way any other engineering property is tested — with metrics that can be compared over time and across organizations, not just a qualitative "we found some issues" report. It defines **security channels** it expects a thorough assessment to cover:

| Channel | Covers |
|---|---|
| Human Security | Social engineering resistance |
| Physical Security | Facility access, physical controls |
| Wireless Security | Wi-Fi, Bluetooth, RF-based communications |
| Telecommunications Security | Phone systems, PBX, VoIP |
| Data Networks Security | The traditional network/application pentest surface |

## 3. The RAV (Risk Assessment Value)

OSSTMM's scoring model weighs three factors together rather than just counting vulnerabilities:

```
RAV = f(Porosity, Limitations, Controls)

Porosity     → how exposed/reachable the target is (attack surface)
Limitations  → weaknesses, exposures, vulnerabilities present
Controls     → the safeguards actually in place to compensate
```

A target can have a high attack surface (porosity) but still score well if compensating controls are strong — this nuance is similar to why [[Vulnerability_Management]] warns against relying on raw CVSS score alone.

## 4. OSSTMM vs PTES — When Each Fits

| Aspect | PTES | OSSTMM |
|---|---|---|
| Structure | Phase-based lifecycle | Channel-based, metrics-driven |
| Output emphasis | Narrative findings + exploitation proof | Quantified score (RAV) comparable over time |
| Best fit | Most standard client-facing pentests | Organizations wanting trend tracking across repeated assessments, or a rigorous audit-style engagement |

In practice, many real-world engagements borrow PTES's phase structure for execution while optionally applying OSSTMM-style metrics for the final scoring/reporting — the two are complementary, not mutually exclusive.

## 5. Interview Questions

1. What does OSSTMM emphasize that distinguishes it from PTES? → **Quantifiable, metrics-driven scoring (the RAV) over a purely phase-based, narrative approach**
2. What three factors combine to form the RAV? → **Porosity (attack surface), Limitations (vulnerabilities present), and Controls (compensating safeguards)**
3. Name the five security channels OSSTMM expects a thorough assessment to cover. → **Human, Physical, Wireless, Telecommunications, and Data Networks security**
4. Why might an organization prefer OSSTMM's approach for repeated, ongoing assessments? → **Its numeric RAV score is directly comparable across engagements over time, unlike a purely narrative findings report**

## 6. Key Points

- OSSTMM is **metrics-driven** — its output is a quantifiable score (RAV), not just a findings list.
- RAV combines **porosity** (exposure), **limitations** (weaknesses), and **controls** (safeguards) — attack surface alone doesn't determine the score.
- It defines five testing channels: human, physical, wireless, telecommunications, and data networks — broader than a typical network/web pentest scope.
- OSSTMM and PTES are **complementary** — PTES for lifecycle structure, OSSTMM for measurable, trackable scoring.

---
*Related: [[PTES_Overview]], [[Vulnerability_Management]], [[VAPT_Engagement_Lifecycle]]*
