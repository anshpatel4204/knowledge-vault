## 1. Introduction

This capstone ties every note in this folder into one realistic API assessment workflow, maps the folder onto the OWASP API Security Top 10, and hands off into Folder 6, where the focus shifts from external APIs to internal, credentialed Windows/AD environments.

## 2. End-to-End Workflow

```
1. Recon                    → API_Recon_and_Fingerprinting (find docs, endpoints, versions)
2. Build test collection     → Postman_and_API_Testing_Tooling (import spec, route through Burp)
3. Authentication testing    → Broken_Authentication_in_APIs, JWT_Attacks_and_API_Token_Security
4. Authorization testing:
   - Object-level             → Broken_Object_Level_Authorization_BOLA
   - Function-level           → Broken_Function_Level_Authorization
   - Field-level               → Excessive_Data_Exposure_and_Mass_Assignment
5. Abuse/resilience testing   → Unrestricted_Resource_Consumption_and_Rate_Limiting
6. GraphQL-specific pass      → GraphQL_Security_Testing (if applicable to the target)
7. Chain findings, score, and report   → Folder 14, Reporting_and_Risk_Scoring
```

## 3. A Worked Chaining Example

```
1. Recon finds an exposed /swagger.json, revealing an undocumented v1 endpoint still live (API_Recon_and_Fingerprinting)
2. The v1 endpoint's JWT validation doesn't enforce the expected algorithm (JWT_Attacks_and_API_Token_Security)
3. A forged token with "role": "admin" is accepted
4. That forged admin token is used against a function-level endpoint with no
   additional server-side role check (Broken_Function_Level_Authorization)
5. Final impact: an "informational" leftover API version becomes full administrative
   access, chained through a token-forgery bug and a missing function-level check
```

## 4. Mapping This Folder to the OWASP API Security Top 10

| OWASP API Top 10 Category (representative) | Covered In |
|---|---|
| Broken Object Level Authorization | [[Broken_Object_Level_Authorization_BOLA]] |
| Broken Authentication | [[Broken_Authentication_in_APIs]], [[JWT_Attacks_and_API_Token_Security]] |
| Broken Object Property Level Authorization | [[Excessive_Data_Exposure_and_Mass_Assignment]] |
| Unrestricted Resource Consumption | [[Unrestricted_Resource_Consumption_and_Rate_Limiting]] |
| Broken Function Level Authorization | [[Broken_Function_Level_Authorization]] |
| Improper Inventory Management | [[API_Recon_and_Fingerprinting]] (undocumented/old versions) |

## 5. Where This Folder Hands Off Next

APIs are still an *external* or perimeter-adjacent attack surface. Once an initial foothold exists — whether through an API compromise, a web app finding, or direct network access — testing shifts to what an attacker can do **inside** the network, starting with Folder 6, Active_Directory_and_Internal_Pentesting.

## 6. Interview Questions

1. Why does this folder recommend testing object-level, function-level, and field-level authorization as three distinct passes? → **Each operates at a different granularity — which object, which action/endpoint, and which specific fields of an object — and an API can pass one type of check while still failing another, so each needs its own dedicated test pass**
2. In the worked chaining example, what turned a low-severity recon finding into full administrative access? → **An undocumented, still-live old API version had weak JWT algorithm validation allowing token forgery, and the forged admin token then hit a function-level endpoint with no additional server-side role check — three individually moderate issues chaining into full admin access**
3. How does this folder's workflow map onto the general PTES phases from Folder 1? → **Recon and tooling setup correspond to Intelligence Gathering; the authentication/authorization/abuse testing passes correspond to Vulnerability Analysis and Exploitation; chaining and scoring findings corresponds to Reporting**
4. Why does testing shift focus after this folder from external APIs to internal Active Directory environments? → **Once a foothold is gained through an API or web app compromise, or a direct network position is achieved, the natural next question in a real engagement becomes what that access allows an attacker to reach and escalate to inside the internal network**

## 7. Key Points

- A full API assessment layers **authentication, object-level, function-level, and field-level authorization testing**, plus abuse/resilience checks — no single test class is sufficient alone.
- Real impact comes from **chaining** — an "informational" leftover API version can become full compromise when combined with a token or authorization bug.
- This folder maps directly onto the **OWASP API Security Top 10**, giving a standard vocabulary for writing up findings.
- This folder hands off into **Folder 6, Active_Directory_and_Internal_Pentesting**, once external/perimeter testing is complete.

---
*Related: [[API_Recon_and_Fingerprinting]], [[Web_App_Exploitation_Workflow]], [[VAPT_Engagement_Lifecycle]]*
