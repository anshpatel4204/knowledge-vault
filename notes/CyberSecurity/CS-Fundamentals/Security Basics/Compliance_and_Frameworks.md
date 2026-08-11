## 1. Introduction

**Compliance** is the act of meeting legally mandated or contractually required security/privacy obligations. **Frameworks** provide structured, often voluntary, best-practice guidance for building a security program. The two overlap heavily in practice — many frameworks exist specifically to help organizations *achieve* compliance — but they're conceptually distinct: compliance is about meeting a required bar; frameworks are tools/methodologies for getting there (and often exceeding it).

## 2. Compliance vs Security — An Important Distinction

**Being compliant does not automatically mean being secure**, and vice versa. Compliance defines a *minimum* required baseline, often shaped by legal/regulatory/contractual concerns rather than genuine risk-based prioritization — an organization can pass every compliance checkbox while still having exploitable gaps outside that checklist's scope. Mature programs treat compliance as a **floor**, not a **ceiling**.

## 3. Major Regulatory/Legal Frameworks

### GDPR (General Data Protection Regulation)
EU regulation governing the processing of personal data of EU residents, regardless of where the processing organization is located.

- **Key principles:** lawfulness/fairness/transparency, purpose limitation, data minimization, accuracy, storage limitation, integrity/confidentiality, accountability.
- **Individual rights:** access, rectification, erasure ("right to be forgotten"), data portability, objection to processing.
- **Breach notification:** must notify the relevant supervisory authority within **72 hours** of becoming aware of a qualifying breach.
- **Penalties:** up to €20 million or 4% of global annual revenue, whichever is higher.

### HIPAA (Health Insurance Portability and Accountability Act)
US law governing protection of **PHI (Protected Health Information)**.

- **Privacy Rule** — governs use/disclosure of PHI.
- **Security Rule** — requires administrative, physical, and technical safeguards specifically for **electronic** PHI (ePHI).
- **Breach Notification Rule** — requires notifying affected individuals (and HHS) following a breach.

### PCI-DSS (Payment Card Industry Data Security Standard)
Contractual (not government-legal) standard required by payment card brands for any organization that stores, processes, or transmits cardholder data.

- **12 core requirements**, spanning network security, access control, encryption, monitoring, and policy — includes concepts like network segmentation to reduce PCI scope, and explicitly allows documented compensating controls (see [[Risk_Management]]) when a specific requirement can't be met directly.

### SOX (Sarbanes-Oxley Act)
US law focused on financial reporting integrity for publicly traded companies — includes IT controls relevant to the accuracy and security of financial data/systems.

## 4. Major Security Frameworks

### NIST Cybersecurity Framework (CSF)
A voluntary, widely-adopted framework organized around five (now six, with the 2.0 update adding "Govern") core functions:

| Function | Purpose |
|---|---|
| Govern | Establish and monitor cybersecurity risk management strategy (added in CSF 2.0) |
| Identify | Understand assets, risks, and business context |
| Protect | Implement safeguards to limit/contain impact |
| Detect | Identify occurrence of a security event |
| Respond | Take action on a detected incident |
| Recover | Restore capabilities/services after an incident |

### ISO/IEC 27001
An international standard for **Information Security Management Systems (ISMS)** — a certifiable standard (unlike NIST CSF, which is a voluntary reference framework) demonstrating a formal, auditable security management process. Includes **Annex A**, a list of security controls organizations select from based on their risk assessment.

### NIST SP 800-53
A detailed catalog of security and privacy controls, primarily used by US federal systems (and widely referenced beyond government) — far more granular/prescriptive than the higher-level NIST CSF.

### CIS Controls (Center for Internet Security)
A prioritized, practical set of specific technical safeguards (organized into Implementation Groups by organizational maturity/resources), often praised for being more directly actionable than higher-level frameworks.

### SOC 2 (System and Organization Controls 2)
An auditing framework (via AICPA) resulting in an attestation report, evaluated against five **Trust Service Criteria**: Security, Availability, Processing Integrity, Confidentiality, Privacy. Commonly requested by enterprise customers evaluating a SaaS vendor's security posture — a **Type I** report assesses controls at a point in time; a **Type II** report assesses their operating effectiveness over a period (typically 6-12 months), which is generally considered more meaningful.

## 5. Framework Comparison

| Framework | Type | Certifiable? | Primary Focus |
|---|---|---|---|
| NIST CSF | Voluntary framework | No | High-level risk management structure |
| ISO 27001 | International standard | Yes | Formal ISMS, auditable certification |
| NIST 800-53 | Control catalog | No (used for FedRAMP/ATO processes) | Detailed federal-grade controls |
| CIS Controls | Practical control list | No | Actionable, prioritized technical safeguards |
| PCI-DSS | Contractual standard | Yes (via QSA assessment) | Payment card data protection |
| SOC 2 | Audit/attestation | Yes (attestation report) | Vendor/service organization trust |

## 6. Why Multiple Frameworks Often Apply Simultaneously

A single organization frequently must satisfy several overlapping frameworks at once — e.g., a healthcare SaaS company processing payments might need **HIPAA** (health data), **PCI-DSS** (payment data), **SOC 2** (customer trust/vendor requirements), and voluntarily align with **NIST CSF** for overall program structure. **Control mapping** (identifying where requirements overlap across frameworks) reduces duplicate effort.

## 7. Audits and Assessments

| Term | Description |
|---|---|
| Internal Audit | Performed by the organization's own audit function |
| External/Third-Party Audit | Performed by an independent outside auditor, often required for certifications (ISO 27001, PCI-DSS, SOC 2) |
| Gap Assessment | Identifies where current practices fall short of a target framework's requirements, before pursuing formal certification |
| Continuous Compliance | Ongoing, automated monitoring against requirements rather than relying solely on point-in-time annual audits |

## 8. Interview Questions

1. Does being compliant guarantee an organization is secure? → **No — compliance defines a minimum required baseline, often shaped by legal/contractual concerns; genuine security requires going beyond just meeting checklist requirements**
2. What's the key difference between NIST CSF and ISO 27001? → **NIST CSF is a voluntary, high-level risk management framework; ISO 27001 is a certifiable international standard for a formal ISMS**
3. What's required under GDPR's breach notification rule? → **Notifying the relevant supervisory authority within 72 hours of becoming aware of a qualifying breach**
4. What's the difference between a SOC 2 Type I and Type II report? → **Type I assesses controls at a single point in time; Type II assesses their operating effectiveness over a period (typically 6-12 months), generally considered more meaningful**
5. What does PCI-DSS govern, and is it a government regulation? → **It governs protection of payment cardholder data; it's a contractual industry standard from the payment card brands, not a government law**
6. Why might a single organization need to comply with multiple frameworks simultaneously? → **Different frameworks address different data types/relationships (health data, payment data, customer trust) that can all apply to the same organization at once**

## 9. Key Points

- **Compliance** = meeting a required legal/contractual baseline; **frameworks** = structured guidance for building a security program — related but distinct.
- Major regulations: **GDPR** (EU personal data), **HIPAA** (US health data), **PCI-DSS** (payment card data), **SOX** (financial reporting).
- Major frameworks: **NIST CSF** (voluntary, high-level), **ISO 27001** (certifiable ISMS), **NIST 800-53** (detailed federal controls), **CIS Controls** (practical/actionable), **SOC 2** (vendor trust attestation).
- Compliance should be treated as a **floor, not a ceiling** — passing an audit doesn't mean all real-world risk has been addressed.
- Organizations frequently satisfy **multiple overlapping frameworks** simultaneously, using control mapping to reduce duplicate effort.

---
*Related: [[Risk_Management]], [[Security_Policies_and_Governance]], [[Data_Security_and_Classification]]*
