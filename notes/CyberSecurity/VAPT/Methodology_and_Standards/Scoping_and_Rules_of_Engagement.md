## 1. Introduction

The **Rules of Engagement (RoE)** is the single document that makes a penetration test legal rather than a crime — see [[Legal_and_Ethical_Considerations]] for why that line matters. Scoping is the process of negotiating and documenting exactly what that authorization covers before a single packet of active testing is sent.

## 2. What Belongs in a Scoping Document

| Element | Defines |
|---|---|
| In-scope assets | IP ranges, domains, applications, physical locations explicitly authorized for testing |
| Out-of-scope assets | Explicitly excluded systems (e.g., third-party-hosted services, production payment systems) |
| Testing type | Black/gray/white box (see [[Types_of_Pentest_Black_Grey_White_Box]]) |
| Testing window | Start/end dates, and permitted hours (e.g., business hours vs overnight-only) |
| Techniques allowed/prohibited | Whether DoS-style testing, social engineering, or physical access attempts are permitted |
| Emergency contacts | Who to call if testing causes an unexpected outage or a real incident is discovered mid-test |
| Data handling rules | How discovered sensitive data (PII, credentials) must be stored, reported, and destroyed after the engagement |

## 3. Why "Get It in Writing" Isn't Optional

Testing a system without explicit, signed authorization is illegal under laws like the US Computer Fraud and Abuse Act (CFAA) or the UK Computer Misuse Act — regardless of good intentions. The RoE, signed by someone with actual authority over the systems in scope, is the tester's legal protection and the single artifact that separates authorized testing from unauthorized intrusion.

```
No signed RoE  →  Testing = unauthorized access, a crime
Signed RoE     →  Testing = authorized, protected professional work
                   (strictly within the boundaries the RoE defines)
```

## 4. Common Scoping Pitfalls

| Pitfall | Consequence |
|---|---|
| Vague IP range ("the office network") | Tester accidentally hits an out-of-scope or third-party-owned system |
| No emergency contact defined | A production outage during testing has no fast escalation path |
| Undefined social engineering boundaries | Legal/HR complications if employees are phished without prior sign-off from leadership |
| No agreement on cloud-hosted assets | Testing a cloud resource can violate the cloud provider's own acceptable-use policy unless separately authorized (AWS/Azure/GCP each have their own pentest authorization process) |

## 5. Interview Questions

1. Why is a signed Rules of Engagement document legally essential before testing begins? → **Without explicit written authorization, testing is illegal under laws like the CFAA regardless of intent — the RoE is what legally separates a pentester from an attacker**
2. Name three elements that should always be included in a scoping document. → **Any three of: in-scope/out-of-scope assets, testing type, testing window, allowed/prohibited techniques, emergency contacts, data handling rules**
3. Why might testing a cloud-hosted asset require more than just the client's own sign-off? → **Cloud providers (AWS, Azure, GCP) have their own acceptable-use policies and often require separate authorization before pentesting resources hosted on their infrastructure**
4. What should an RoE define regarding an unexpected production outage during testing? → **Emergency contacts and an escalation process, so the client can be reached immediately if testing activity causes real business impact**

## 6. Key Points

- The **RoE** is the legal foundation of every engagement — it must be signed by someone with actual authority over the in-scope systems before testing starts.
- A thorough scope covers in/out-of-scope assets, testing type, timing window, allowed techniques, emergency contacts, and data handling.
- **Cloud assets** often need separate authorization from the cloud provider itself, beyond the client's own sign-off.
- Vague scoping is the most common source of real-world engagement problems — precision here prevents legal and operational issues later.

---
*Related: [[Legal_and_Ethical_Considerations]], [[Types_of_Pentest_Black_Grey_White_Box]], [[VAPT_Engagement_Lifecycle]], [[PTES_Overview]]*
