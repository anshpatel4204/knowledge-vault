## 1. Introduction

Web applications are one of the most exposed and frequently attacked surfaces in modern IT — publicly reachable, complex, and often built on layers of third-party code. The **OWASP Top 10** (maintained by the Open Web Application Security Project) is the industry-standard reference ranking the most critical web application security risks, updated periodically as attack trends evolve. This note covers the core vulnerability classes it represents.

## 2. Injection (SQL Injection and Beyond)

Occurs when untrusted user input is inserted into a command/query interpreter without proper validation or parameterization, allowing an attacker to alter the intended logic.

### SQL Injection (SQLi)
```sql
-- Vulnerable query built via string concatenation:
SELECT * FROM users WHERE username = '$input' AND password = '$pass'

-- Attacker input: ' OR '1'='1
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = ''
-- Bypasses authentication entirely
```

**Types:** classic (direct data retrieval), blind (inferring data via true/false responses), time-based blind (inferring data via response delay), union-based (combining results with attacker-controlled queries).

**Mitigation:** parameterized queries/prepared statements (the primary fix), ORM frameworks, input validation, least-privilege database accounts, WAF as a secondary layer.

### Other Injection Types
- **Command Injection** — untrusted input passed to an OS shell command.
- **LDAP Injection** — manipulating LDAP queries similarly to SQLi.
- **XML/XXE (XML External Entity) Injection** — exploiting XML parsers that process external entity references, potentially reading local files or triggering SSRF.

## 3. Cross-Site Scripting (XSS)

Injects malicious client-side scripts into web pages viewed by other users, executing in their browser session with their privileges.

| Type | Description |
|---|---|
| Stored (Persistent) XSS | Malicious script is saved server-side (e.g., in a comment field) and served to every visitor |
| Reflected XSS | Malicious script is embedded in a request (e.g., a URL parameter) and reflected back in the immediate response — requires tricking a victim into clicking a crafted link |
| DOM-based XSS | Vulnerability exists entirely in client-side JavaScript manipulating the DOM, without the malicious payload ever touching the server |

**Impact:** session/cookie theft (see [[Common_Cyber_Attacks]]), credential harvesting via fake forms, defacement, malware delivery.

**Mitigation:** output encoding/escaping (context-aware), Content Security Policy (CSP), input validation, HttpOnly cookie flags (prevents JavaScript from reading session cookies).

## 4. Cross-Site Request Forgery (CSRF)

Tricks an authenticated victim's browser into unknowingly submitting a malicious request to a site where they're currently logged in, exploiting the fact that browsers automatically attach cookies to requests regardless of which site initiated them.

```html
<!-- Hosted on an attacker's site, auto-submits if the victim is logged into bank.com -->
<img src="https://bank.com/transfer?to=attacker&amount=1000">
```

**Mitigation:** anti-CSRF tokens (unique, unpredictable per-session/per-request values validated server-side), SameSite cookie attribute, re-authentication for sensitive actions.

## 5. Broken Access Control

Failures in enforcing what authenticated users are actually permitted to do — consistently ranked among the most critical/common real-world web vulnerabilities.

| Example | Description |
|---|---|
| IDOR (Insecure Direct Object Reference) | Changing an ID in a URL/request (`/invoice?id=1001` → `id=1002`) to access another user's data without authorization checks |
| Missing Function-Level Access Control | An admin-only feature is reachable by a regular user simply because the UI hides it, not because the server actually enforces it |
| Privilege Escalation via Parameter Tampering | Modifying a request parameter (e.g., `role=user` → `role=admin`) that the server trusts without re-validating |

**Mitigation:** enforce authorization checks server-side on every request, never rely on hiding UI elements as a security control, deny-by-default access policies.

## 6. Security Misconfiguration

Insecure default settings, unnecessary features enabled, verbose error messages leaking stack traces/internals, missing security headers, or outdated/unpatched components left exposed. One of the most common findings in real-world assessments precisely because it requires no clever exploit — just an unchecked default.

**Mitigation:** hardened deployment baselines, removing unused features/accounts, disabling detailed error messages in production, regular configuration audits.

## 7. Vulnerable and Outdated Components

Using libraries, frameworks, or dependencies with known vulnerabilities — a component-level version of [[Vulnerability_Management]] applied specifically to the application's software supply chain.

**Mitigation:** dependency scanning (SCA — Software Composition Analysis tools), maintaining an SBOM (Software Bill of Materials), timely patching of third-party libraries, not just first-party code.

## 8. Identification and Authentication Failures

Weaknesses in login, session management, or credential handling — weak password policies, missing MFA, predictable session identifiers, session tokens that don't expire/rotate properly. See [[Identity_and_Access_Management]] for the full defensive picture.

## 9. Server-Side Request Forgery (SSRF)

Tricks a server into making requests to unintended destinations — often internal-only resources the attacker couldn't otherwise reach directly (cloud metadata endpoints, internal admin panels, other internal services). Frequently arises from features that fetch a URL supplied by the user (e.g., "import from URL," webhook configuration, image proxy).

**Mitigation:** allow-list permitted destinations, block requests to internal/private IP ranges, disable unnecessary URL-fetching features.

## 10. Insecure Deserialization

Occurs when an application deserializes untrusted data without validation, potentially allowing an attacker to manipulate the serialized object to execute arbitrary code, alter application logic, or escalate privileges — particularly dangerous in languages/frameworks with powerful object serialization (Java, PHP, Python pickle).

## 11. Security Logging and Monitoring Failures

Insufficient logging/alerting on security-relevant events means breaches can go undetected for extended periods, delaying response and increasing damage — connects directly to [[Incident_Response]] and [[Log_Management]] (Linux folder). A technically preventable attack that succeeds anyway is bad; one that succeeds AND goes unnoticed for months is far worse.

## 12. Web Attack Comparison Table

| Attack | Exploits | Primary Fix |
|---|---|---|
| SQL Injection | Unsanitized input in database queries | Parameterized queries |
| XSS | Unsanitized input rendered in browser | Output encoding, CSP |
| CSRF | Browsers auto-attaching cookies to cross-site requests | Anti-CSRF tokens, SameSite cookies |
| IDOR | Missing server-side authorization checks | Enforce access control on every request |
| SSRF | Server making attacker-influenced requests | Allow-list destinations, block internal IP ranges |

## 13. Interview Questions

1. What's the core fix for SQL injection, and why does simple input "sanitization" alone often fall short? → **Parameterized queries/prepared statements — because sanitization/escaping can be bypassed by edge cases and encoding tricks that a proper query structure avoids entirely**
2. What's the difference between stored and reflected XSS? → **Stored XSS persists server-side and affects every visitor; reflected XSS requires tricking a victim into clicking a crafted link that reflects the payload back immediately**
3. How does CSRF actually work, given the attacker never sees the victim's cookies? → **The victim's browser automatically attaches their existing session cookies to a forged request triggered by the attacker's page, without the attacker needing to know the cookie value**
4. What is IDOR, and what's the underlying failure that causes it? → **Insecure Direct Object Reference — accessing another user's data by modifying an ID, caused by missing server-side authorization checks**
5. What makes SSRF dangerous even against a well-firewalled internal network? → **The request originates from the trusted server itself, which may have network access to internal-only resources the attacker couldn't reach directly**
6. Why is "vulnerable and outdated components" its own OWASP category rather than being covered by general vulnerability management? → **It specifically highlights that most real applications are built on substantial third-party code, and that supply chain/dependency risk needs the same rigor as first-party code**

## 14. Key Points

- **Injection (SQLi)** and **XSS** remain foundational, high-impact vulnerability classes — fixed by parameterized queries and output encoding/CSP respectively.
- **CSRF** exploits automatic cookie attachment; **SSRF** exploits a trusted server making attacker-influenced requests — easy to confuse by name, very different mechanisms.
- **Broken access control (including IDOR)** is consistently one of the most common real-world findings — always enforce authorization server-side.
- **Security misconfiguration** and **vulnerable/outdated components** require no clever exploit, just diligence — often the actual root cause behind headline breaches.
- **Insufficient logging/monitoring** turns a contained incident into a prolonged, undetected breach — ties directly into [[Incident_Response]].

---
*Related: [[Common_Cyber_Attacks]], [[Vulnerability_Management]], [[Identity_and_Access_Management]], [[Security_Testing_Types]]*
