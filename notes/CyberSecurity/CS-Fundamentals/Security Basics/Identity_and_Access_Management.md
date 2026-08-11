## 1. Introduction

**Identity and Access Management (IAM)** encompasses the policies, processes, and technologies for managing digital identities and controlling what those identities can access. Where [[AAA]] (in the Networking folder) covers the core Authentication/Authorization/Accounting mechanism, this note goes broader — covering the identity lifecycle, modern authentication architectures (SSO, federation), and access control models in depth.

## 2. The Identity Lifecycle — Joiner, Mover, Leaver (JML)

| Stage | Description |
|---|---|
| Joiner (Provisioning) | A new user/employee is granted an identity and appropriate initial access |
| Mover | An existing user changes roles — access should be adjusted accordingly (old access revoked, new access granted) |
| Leaver (Deprovisioning) | A user departs — all access must be promptly revoked |

**Why this matters:** delayed deprovisioning is one of the most common, preventable sources of unauthorized access — former employees or contractors retaining active credentials long after they should have been revoked is a recurring real-world audit finding.

## 3. Authentication Factors (Recap and Depth)

See [[AAA]] for the base factor categories. In IAM specifically:

| Factor | Category | Strength Considerations |
|---|---|---|
| Password | Knowledge | Weakest alone — vulnerable to phishing, reuse, brute force |
| OTP (Time-based/HOTP) | Possession | Strong when combined with another factor; TOTP apps (Google Authenticator) more resistant to interception than SMS |
| Push Notification | Possession | Convenient, but vulnerable to "MFA fatigue" attacks (spamming approval requests until a user accidentally approves) |
| Hardware Security Key (FIDO2/WebAuthn) | Possession | Among the strongest — phishing-resistant, since the key cryptographically verifies the actual origin/domain |
| Biometrics | Inherence | Convenient, but can't be "changed" if compromised, and quality varies significantly by implementation |

## 4. Multi-Factor Authentication (MFA) — Depth

Combining two or more **distinct factor categories** significantly reduces the risk of a compromised credential leading to account takeover. Critically, using two factors from the *same* category (e.g., a password plus a security question, both "something you know") does **not** count as true MFA — it's just two knowledge factors, both vulnerable to similar attack methods.

**MFA Fatigue / Push Bombing:** an attacker with a stolen password repeatedly triggers push notification MFA prompts, hoping the victim eventually approves one out of annoyance or confusion — mitigated by number-matching MFA (requiring the user to enter a displayed number, not just tap "approve") and rate-limiting prompts.

## 5. Single Sign-On (SSO)

Allows a user to authenticate **once** and gain access to multiple independent applications/systems without re-authenticating for each.

**Benefits:** improved user experience, centralized authentication policy enforcement (MFA, conditional access), simplified deprovisioning (disable one account, lose access everywhere).

**Risk:** SSO also concentrates risk — a compromised SSO identity potentially grants access to every connected application ("keys to the kingdom"), making strong authentication on the SSO identity itself especially critical.

## 6. Federation

Extends SSO **across organizational boundaries** — allowing a user authenticated by one organization's identity provider to access resources belonging to a different, trusted organization, without needing separate credentials for each.

| Protocol | Description |
|---|---|
| SAML (Security Assertion Markup Language) | XML-based, widely used for enterprise SSO/federation |
| OAuth 2.0 | An **authorization** framework — grants limited access to resources without sharing credentials (e.g., "Allow App X to access your Google Calendar") |
| OpenID Connect (OIDC) | An **authentication** layer built on top of OAuth 2.0, adding identity verification |

**Common confusion — OAuth vs OIDC:** OAuth 2.0 answers "what can this application do on my behalf" (authorization); OpenID Connect answers "who is this user" (authentication) — they're frequently used together but solve different problems.

## 7. Access Control Models — Deeper Dive

(Introduced briefly under [[AAA]] in the Networking folder — expanded here.)

| Model | Description | Best Suited For |
|---|---|---|
| DAC (Discretionary) | Resource owner decides access | Flexible, small-scale, less regulated environments |
| MAC (Mandatory) | System-enforced labels/clearances, users can't override | High-security/government/military environments |
| RBAC (Role-Based) | Permissions tied to roles, users assigned to roles | Most common in enterprise environments — scalable, auditable |
| ABAC (Attribute-Based) | Access decisions based on a combination of attributes (user department, resource sensitivity, time of day, location) | Complex, dynamic, context-sensitive environments |
| PBAC (Policy-Based) | Access governed by centrally defined, often machine-readable policies — closely related to ABAC | Zero Trust architectures, cloud-native environments |

## 8. Privileged Access Management (PAM)

A specialized subset of IAM focused specifically on **privileged accounts** (admin/root/service accounts) — which carry disproportionate risk if compromised.

| PAM Technique | Description |
|---|---|
| Just-In-Time (JIT) Access | Privileged access is granted only when needed, for a limited time window, rather than standing indefinitely |
| Privileged Session Monitoring | Recording/auditing what privileged users actually do during elevated sessions |
| Password Vaulting | Storing privileged credentials in a secure, access-controlled vault rather than distributing them directly, often with automatic rotation |
| Break-Glass Accounts | Emergency-access accounts, tightly controlled and heavily audited, used only when normal access paths fail |

## 9. Directory Services

Centralized systems storing identity and access information.

| System | Notes |
|---|---|
| Active Directory (AD) | Microsoft's directory service, dominant in Windows enterprise environments, uses Kerberos and LDAP |
| LDAP | Open, cross-platform protocol for accessing/maintaining directory information (see [[AAA]] in Networking folder) |
| Azure AD / Entra ID | Cloud-based identity platform, increasingly central to modern hybrid/cloud-first IAM |

## 10. Access Reviews and Certification

Periodic, formal review of who has access to what — ensuring access still matches current role/need (catching stale, over-provisioned, or orphaned access left over from role changes or incomplete offboarding). A core control in most compliance frameworks (see [[Compliance_and_Frameworks]]).

## 11. Interview Questions

1. Why doesn't a password plus a security question count as true MFA? → **Both are "something you know" — the same factor category — not two distinct factor types**
2. What's the difference between OAuth 2.0 and OpenID Connect? → **OAuth 2.0 handles authorization (what an app can do on your behalf); OIDC, built on top of OAuth, handles authentication (verifying who the user is)**
3. What is MFA fatigue/push bombing, and how is it mitigated? → **Repeatedly sending MFA approval prompts hoping the victim accidentally approves one; mitigated with number-matching and rate-limiting**
4. What's the difference between RBAC and ABAC? → **RBAC assigns permissions via roles; ABAC makes access decisions based on a combination of dynamic attributes (user, resource, environment)**
5. What is Just-In-Time (JIT) privileged access? → **Granting elevated access only for a limited time window when actually needed, rather than maintaining standing privileged access indefinitely**
6. Why is prompt deprovisioning during the "leaver" stage so critical? → **Delayed revocation of a departed user's access is a common, preventable source of unauthorized access**

## 12. Key Points

- IAM covers the full identity lifecycle (**Joiner-Mover-Leaver**) — not just point-in-time authentication.
- True **MFA** requires factors from genuinely distinct categories; **push bombing** exploits convenience-based MFA without number matching.
- **SSO** improves UX and centralizes control but concentrates risk; **federation** (SAML/OIDC) extends this across organizational boundaries.
- **OAuth 2.0** = authorization; **OpenID Connect** = authentication built on top of it — a frequently confused distinction.
- **RBAC** (role-based) is the enterprise default; **ABAC** (attribute-based) suits more dynamic, context-sensitive access decisions.
- **PAM** (Privileged Access Management) — JIT access, session monitoring, credential vaulting — specifically hardens the highest-risk accounts.

---
*Related: [[AAA]] (Networking folder), [[Security_Controls]], [[Defense_in_Depth_and_Zero_Trust]], [[Social_Engineering]]*
