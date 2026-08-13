## 1. Introduction

The **OWASP Testing Guide (OTG)** is the de facto standard for web application penetration testing specifically — narrower in scope than PTES or OSSTMM, but far deeper on its one focus area. It's published by OWASP, the same organization behind the OWASP Top 10 already covered in [[Web_Application_Attacks_OWASP_Top10]], and this note is the bridge between that theory and Folder 4's hands-on exploitation.

## 2. Structure of the Guide

The OTG organizes testing into categories covering the full web application attack surface, not just the Top 10 list:

| Category | Focus |
|---|---|
| Information Gathering | Fingerprinting the application, framework, and infrastructure |
| Configuration and Deployment Management | Misconfigured servers, exposed admin interfaces, outdated components |
| Identity Management | Registration, account provisioning, username enumeration |
| Authentication | Login mechanisms, password policy, credential handling |
| Authorization | Access control, privilege escalation within the app |
| Session Management | Cookies, tokens, session fixation/hijacking |
| Input Validation | Injection flaws — SQLi, XSS, command injection, and more |
| Error Handling | Information leakage through error messages/stack traces |
| Cryptography | TLS configuration, weak/broken crypto implementation |
| Business Logic | Flaws in the application's intended workflow, not just its code |
| Client-Side Testing | DOM-based XSS, client-side storage, CORS misconfiguration |

## 3. The OWASP Testing Guide vs the OWASP Top 10

| Aspect | OWASP Top 10 | OWASP Testing Guide |
|---|---|---|
| Purpose | Awareness — the 10 most critical, common risk categories | Methodology — a full, category-by-category testing process |
| Scope | A prioritized subset | Comprehensive coverage across every category above |
| Use case | Quick reference, prioritization, training | Structuring an actual full web application pentest |

The Top 10 tells you what's *most likely* to matter; the Testing Guide tells you *how to systematically check everything*, including issues that don't make the Top 10 in a given year.

## 4. Why It Matters for This Vault

Every note in Folder 4 (Web_Application_Pentesting) — SQL injection, XSS, IDOR, business logic flaws, and the rest — maps onto one of these OTG categories. Treat this note as the table of contents; the actual exploitation techniques live in that folder.

## 5. Interview Questions

1. How does the OWASP Testing Guide differ in purpose from the OWASP Top 10? → **The Top 10 is a prioritized awareness list of the most critical risks; the Testing Guide is a full methodology covering every category systematically, not just the top ones**
2. Name three testing categories in the OTG beyond injection flaws. → **Any three of: Information Gathering, Configuration and Deployment Management, Identity Management, Authentication, Authorization, Session Management, Error Handling, Cryptography, Business Logic, Client-Side Testing**
3. Why is Business Logic testing distinct from Input Validation testing? → **Business logic flaws exploit the application's intended workflow itself (e.g., abusing a legitimate discount code multiple times), not a coding defect like unsanitized input**
4. Which OWASP resource would you use to scope a comprehensive web app pentest, vs a quick prioritization discussion with a client? → **The Testing Guide for scoping a comprehensive test; the Top 10 for a quick, high-level prioritization conversation**

## 6. Key Points

- The OWASP Testing Guide is the **web-application-specific** counterpart to PTES/OSSTMM's broader engagement scope.
- It organizes testing into categories — information gathering, auth, session management, input validation, business logic, and more — far beyond just the Top 10 risks.
- The **Top 10 is a prioritized subset**; the **Testing Guide is the comprehensive methodology** — they serve different purposes and are used together.
- Directly sets up Folder 4 (Web_Application_Pentesting), where each OTG category becomes hands-on exploitation technique.

---
*Related: [[Web_Application_Attacks_OWASP_Top10]] (CS-Fundamentals/Security Basics), [[PTES_Overview]], [[NIST_SP_800-115]]*
