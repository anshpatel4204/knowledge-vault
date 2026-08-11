## 1. Introduction

**Security governance** is the framework of leadership, organizational structures, and processes that ensure an organization's security program supports its business objectives and manages risk appropriately. **Policies, standards, procedures, and guidelines** are the documented hierarchy through which governance decisions actually get implemented day-to-day.

## 2. The Documentation Hierarchy

| Document Type | Purpose | Mandatory? |
|---|---|---|
| Policy | High-level statement of management intent and expectations — the "what" and "why" | Yes — mandatory |
| Standard | Specific, mandatory requirements supporting a policy — the measurable "how much/which" | Yes — mandatory |
| Procedure | Detailed, step-by-step instructions for performing a specific task | Yes — mandatory |
| Guideline | Recommended best practices, not strictly mandatory — flexible advice | No — discretionary |
| Baseline | A minimum, uniform level of security all systems of a given type must meet | Yes — mandatory |

**Example chain:** Policy: "All company data must be protected against unauthorized access." → Standard: "All laptops must use full-disk encryption with AES-256." → Procedure: "Steps 1-7 to enable BitLocker on a company Windows laptop." → Guideline: "Consider enabling additional startup PIN protection for extra security."

## 3. Common Security Policy Types

| Policy | Covers |
|---|---|
| Acceptable Use Policy (AUP) | What employees may/may not do with company systems and data |
| Information Security Policy | The overarching umbrella policy defining the organization's security posture and objectives |
| Password Policy | Password complexity, rotation, MFA requirements |
| Data Classification Policy | How data is categorized and handled based on sensitivity (see [[Data_Security_and_Classification]]) |
| Incident Response Policy | Authority and expectations for handling security incidents (see [[Incident_Response]]) |
| Remote Access Policy | Requirements for accessing company resources remotely (VPN, MFA, device requirements) |
| BYOD (Bring Your Own Device) Policy | Rules governing personal devices used for work purposes |
| Change Management Policy | Process requirements for making changes to production systems |
| Vendor/Third-Party Risk Policy | Requirements for assessing and managing third-party security risk |
| Data Retention Policy | How long different data types must/may be kept before secure disposal |

## 4. Governance Roles and Responsibilities

| Role | Responsibility |
|---|---|
| Board of Directors / Executive Leadership | Sets risk appetite, ultimate accountability for organizational risk |
| CISO (Chief Information Security Officer) | Owns the security program strategically, reports risk to leadership |
| Data Owner | Business-side individual accountable for a specific data set's classification and protection requirements |
| Data Custodian | Technical role responsible for implementing the protections the data owner requires (e.g., IT/security team) |
| System Owner | Accountable for a specific system's operation and security |
| Security Team / Analysts | Implement, monitor, and enforce controls day-to-day |
| All Employees | Responsible for complying with policy and reporting security concerns |

**Data Owner vs Data Custodian — a common exam/interview distinction:** the data owner makes the business decisions (classification, who should access it); the custodian implements the technical controls enforcing those decisions — they are usually different people/roles.

## 5. Security Governance Frameworks (Structural Concept)

Governance frameworks (see [[Compliance_and_Frameworks]] for the full detail on specific standards) generally establish:

1. **Strategic alignment** — security supports business objectives, not the reverse.
2. **Risk management integration** — governance decisions are informed by [[Risk_Management]].
3. **Resource management** — appropriate budget/staffing for the security program.
4. **Performance measurement** — metrics/KPIs tracking whether the program is actually effective.
5. **Value delivery** — demonstrating the security program's contribution, not just its cost.

## 6. Policy Lifecycle

```
Draft → Review → Approval (leadership sign-off) → Publish/Communicate →
Enforce → Periodic Review → Update/Retire
```

Policies that are never reviewed/updated become stale and disconnected from actual practice — a common audit finding is a policy document that no longer reflects how the organization actually operates.

## 7. Enforcement and Exceptions

Policies need a defined enforcement mechanism (technical controls, disciplinary action, or both) to have any real effect beyond being a document. Legitimate business needs sometimes require **documented exceptions** to policy — these should go through a formal approval and risk-acceptance process (see [[Risk_Management]]) rather than being silently ignored, which erodes the policy's authority and creates untracked risk.

## 8. Separation of Duties in Governance

As covered in [[Security_Controls]], governance structures should ensure no single individual has unchecked authority over critical security decisions — audit functions, in particular, are typically kept organizationally independent from the teams whose work they're reviewing, to preserve objectivity.

## 9. Third-Party and Contractual Governance

Security requirements extend into vendor relationships via contracts, **SLAs (Service Level Agreements)**, and **right-to-audit clauses** — governance doesn't stop at the organizational boundary, especially given how much risk flows through third parties (see [[Risk_Management]]).

## 10. Interview Questions

1. What's the difference between a policy and a standard? → **A policy is a high-level statement of intent ("what/why"); a standard defines specific, mandatory requirements supporting that policy ("how much/which")**
2. Is a guideline mandatory? → **No — guidelines are recommended best practices, not strictly enforced requirements, unlike policies/standards/procedures**
3. What's the difference between a data owner and a data custodian? → **The data owner makes business decisions about classification/access; the data custodian technically implements the required protections**
4. Why should policy exceptions go through a formal approval process rather than being silently allowed? → **Undocumented exceptions erode policy authority and create untracked, unmanaged risk**
5. Why is audit typically kept organizationally independent from the teams it reviews? → **To preserve objectivity and prevent conflicts of interest in evaluating that team's own work**
6. What happens to a security program's credibility if policies are never reviewed or updated? → **They become stale and disconnected from actual practice, undermining both compliance and real security effectiveness**

## 11. Key Points

- Documentation hierarchy: **Policy (mandatory, high-level) → Standard (mandatory, specific) → Procedure (mandatory, step-by-step) → Guideline (optional, advisory)**.
- **Data owners** decide classification/access; **data custodians** technically implement the resulting protections — distinct roles.
- Governance assigns clear accountability (CISO, data owners, system owners) and integrates directly with [[Risk_Management]].
- Policies require an **enforcement mechanism** and a **review lifecycle** — an unenforced or stale policy provides false assurance.
- Formal, documented **exceptions** (not silent ignoring) are how legitimate business needs are reconciled with policy requirements.

---
*Related: [[Risk_Management]], [[Compliance_and_Frameworks]], [[Data_Security_and_Classification]], [[Security_Controls]]*
