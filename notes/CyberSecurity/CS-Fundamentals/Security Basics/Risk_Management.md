## 1. Introduction

**Risk management** is the structured process of identifying, assessing, and responding to risks that could affect an organization's assets. Security programs don't (and can't) eliminate all risk — they make deliberate, informed decisions about which risks to address, and how, based on this process. It's the analytical foundation that [[Security_Controls]] selection should be built on.

## 2. Core Terminology

| Term | Definition |
|---|---|
| Asset | Anything of value to the organization — data, systems, reputation, people |
| Threat | Any potential danger that could exploit a vulnerability (a threat actor, a natural disaster, a system failure) |
| Vulnerability | A weakness that could be exploited by a threat |
| Risk | The potential for loss or damage when a threat exploits a vulnerability against an asset |
| Likelihood | The probability that a given threat will actually exploit a vulnerability |
| Impact | The magnitude of harm/loss if the risk is realized |
| Exposure | The extent to which an asset is susceptible to a threat |

**The relationship:** Risk = Threat × Vulnerability × Impact (conceptually — risk exists only where a threat, a vulnerability, and a valuable asset intersect; remove any one factor and the risk disappears).

## 3. Risk Assessment Process

1. **Identify assets** — what needs protecting, and its value/criticality.
2. **Identify threats** — what could go wrong (see [[Threat_Actors_and_Hackers]], natural/environmental threats, system failures).
3. **Identify vulnerabilities** — weaknesses that could be exploited (see [[Vulnerability_Management]]).
4. **Analyze likelihood and impact** — for each threat/vulnerability pairing.
5. **Calculate/estimate risk level** — combining likelihood and impact.
6. **Determine risk treatment** — decide how to respond (see below).
7. **Document and monitor** — risk registers, periodic reassessment.

## 4. Qualitative vs Quantitative Risk Assessment

| Aspect | Qualitative | Quantitative |
|---|---|---|
| Measurement | Subjective scales (Low/Medium/High, or 1-5) | Numerical/monetary values |
| Speed | Faster, easier | Slower, requires more data |
| Precision | Less precise, relies on expert judgment | More precise, supports cost-benefit comparison |
| Communication | Easier for non-technical stakeholders to grasp | Better for justifying specific budget/investment decisions |
| Example output | "This risk is High likelihood, High impact" | "This risk has an Annualized Loss Expectancy of $50,000" |

Most real organizations use a **hybrid approach** — qualitative for broad prioritization, quantitative for high-value/high-visibility risks requiring specific budget justification.

## 5. Quantitative Risk Formulas

| Term | Formula | Meaning |
|---|---|---|
| SLE (Single Loss Expectancy) | Asset Value × Exposure Factor | Expected loss from a single occurrence of the risk |
| ARO (Annualized Rate of Occurrence) | — | How many times per year the event is expected to occur |
| ALE (Annualized Loss Expectancy) | SLE × ARO | Expected yearly monetary loss from the risk |

**Worked example:** an asset worth $200,000, with an exposure factor of 25% for a specific fire risk (SLE = $50,000), expected to occur once every 20 years (ARO = 0.05) → ALE = $50,000 × 0.05 = **$2,500/year**. This directly informs whether a $2,000/year fire suppression system is a cost-justified investment.

## 6. Risk Matrix (Qualitative Example)

|  | Low Impact | Medium Impact | High Impact |
|---|---|---|---|
| **High Likelihood** | Medium | High | Critical |
| **Medium Likelihood** | Low | Medium | High |
| **Low Likelihood** | Low | Low | Medium |

## 7. Risk Treatment Options (The Four T's)

| Option | Description | Example |
|---|---|---|
| Avoid | Eliminate the risk entirely by not engaging in the risky activity | Discontinuing a legacy, unpatchable system |
| Mitigate (Reduce) | Implement controls to reduce likelihood and/or impact | Deploying MFA, patching, encryption |
| Transfer (Share) | Shift the risk (or its financial impact) to a third party | Cyber insurance, outsourcing to a vendor with contractual liability |
| Accept | Acknowledge the risk and choose to take no further action, typically because the cost of treatment exceeds the risk itself | Formally accepting a low-impact, low-likelihood risk with documented sign-off |

**Risk acceptance requires formal documentation** — an undocumented, unacknowledged risk that's simply ignored is a governance failure, not a legitimate risk treatment decision.

## 8. Residual Risk

The risk that **remains** after controls have been applied — no control eliminates risk entirely, so residual risk is always present and must itself be evaluated against the organization's **risk appetite/tolerance** (how much risk leadership is willing to accept).

```
Inherent Risk (before any controls) → apply controls → Residual Risk (after controls)
```

## 9. Risk Appetite vs Risk Tolerance

| Term | Meaning |
|---|---|
| Risk Appetite | The overall amount/type of risk an organization is willing to pursue or retain in pursuit of its objectives — a strategic, board-level statement |
| Risk Tolerance | The acceptable variation/deviation around a specific risk metric — more tactical and granular than appetite |

## 10. Third-Party / Vendor Risk

Organizations inherit risk from vendors, suppliers, and partners with access to their systems/data — managed via vendor risk assessments, security questionnaires, contractual security requirements (SLAs), and ongoing monitoring. Directly relevant given how many real breaches originate through a third party (see [[Common_Cyber_Attacks]] — supply chain attacks).

## 11. Risk Register

A living document tracking identified risks, their assessed likelihood/impact, assigned owner, treatment decision, and current status — the central artifact a risk management program is organized around, reviewed and updated periodically as the environment changes.

## 12. Interview Questions

1. What's the relationship between threat, vulnerability, and risk? → **Risk exists where a threat can exploit a vulnerability against a valuable asset — remove any one factor and the risk disappears**
2. What's the difference between qualitative and quantitative risk assessment? → **Qualitative uses subjective scales (Low/Medium/High); quantitative uses numerical/monetary values like ALE**
3. How do you calculate Annualized Loss Expectancy? → **ALE = SLE (Single Loss Expectancy) × ARO (Annualized Rate of Occurrence)**
4. What are the four risk treatment options? → **Avoid, Mitigate, Transfer, Accept**
5. What is residual risk? → **The risk remaining after controls have been applied — never fully eliminated, always present to some degree**
6. What's the difference between risk appetite and risk tolerance? → **Risk appetite is a strategic, board-level statement of overall willingness to accept risk; risk tolerance is the tactical, granular acceptable variation around a specific risk metric**

## 13. Key Points

- Risk = the intersection of a **threat**, a **vulnerability**, and a valuable **asset**.
- Assessment can be **qualitative** (fast, subjective scales) or **quantitative** (precise, monetary — SLE × ARO = ALE).
- Four treatment options: **Avoid, Mitigate, Transfer, Accept** — acceptance must be formally documented, never just implicitly ignored.
- **Residual risk** always remains after controls are applied and must be weighed against the organization's **risk appetite**.
- Risk management is the analytical foundation that should drive [[Security_Controls]] selection — not the other way around.

---
*Related: [[Security_Controls]], [[Vulnerability_Management]], [[Compliance_and_Frameworks]], [[Business_Continuity_and_Disaster_Recovery]]*
