## 1. Introduction

API authentication typically relies on tokens rather than session cookies, which shifts where the weaknesses show up compared to the browser-based session issues in [[Broken_Authentication_and_Session_Management]] — this note covers the API-specific authentication mechanisms and their common failure points.

## 2. Common API Authentication Mechanisms

| Mechanism | How It Works | Common Weakness |
|---|---|---|
| **API keys** | A static key sent per request (header, query param) | Often long-lived, rarely rotated, sometimes leaked in URLs/logs since query-param keys get logged everywhere |
| **Basic Auth** | Base64-encoded username:password per request | Base64 is trivially reversible — must be paired with HTTPS or credentials are effectively cleartext |
| **OAuth 2.0** | Token issued after an authorization flow, scoped to specific permissions | Misconfigured redirect URIs, overly broad scopes, or improperly validated tokens |
| **JWT (JSON Web Tokens)** | Self-contained signed tokens carrying claims | Covered in depth in [[JWT_Attacks_and_API_Token_Security]] |

## 3. Credential and Key Exposure

| Exposure Point | Why It Happens |
|---|---|
| API keys in URL query strings | Query strings are logged by proxies, browsers, and server access logs by default |
| Keys hardcoded in mobile app / frontend JS | Recon target — see [[API_Recon_and_Fingerprinting]] |
| Keys committed to public repositories | A leading real-world cause of API key compromise |
| Overly long token/key lifetimes | A leaked key remains valid far longer than necessary, widening the exposure window |

## 4. Testing Token Validation

```
1. Send a request with no Authorization header at all — does it still succeed?
2. Send a request with a malformed/truncated token — does the API fail open or closed?
3. Send an EXPIRED token — is expiration actually enforced server-side?
4. Send a token for a completely different, unrelated application, if shared infrastructure is suspected
```

An API that fails "open" on a malformed or missing token (i.e. defaults to treating the request as authenticated, or silently ignores the auth check on an error) is a critical finding — this happens more often than expected in hand-rolled authentication middleware.

## 5. Credential Stuffing and Brute Force Against API Login Endpoints

API login/token-issuance endpoints are just as brute-forceable as a web login form, and are frequently *less* protected because rate limiting and lockout policies are more often applied to the visible web UI than to the underlying API endpoint it calls — see [[Unrestricted_Resource_Consumption_and_Rate_Limiting]].

## 6. Interview Questions

1. Why is Basic Auth considered weak on its own, independent of the credentials' strength? → **The credentials are only Base64-encoded, not encrypted — Base64 is trivially reversible, so without HTTPS the credentials are effectively transmitted in cleartext**
2. Why are API keys placed in URL query strings a particular exposure risk? → **Query strings are commonly logged by default across browsers, proxies, and server access logs, meaning the key can end up persisted in plaintext in multiple log locations far beyond the original request**
3. What does it mean for an API to "fail open" on authentication, and why is it critical? → **The API treats a request as authenticated by default when the token is missing, malformed, or fails validation for some reason, rather than rejecting it — this can grant full access with no valid credentials at all**
4. Why are API login/token endpoints sometimes less protected than the corresponding web login form? → **Rate limiting and lockout protections are often applied at the web UI layer and forgotten on the underlying API endpoint the UI calls, leaving the API directly brute-forceable**

## 7. Key Points

- API authentication mechanisms — **API keys, Basic Auth, OAuth 2.0, JWTs** — each carry their own distinct common weaknesses.
- **Query-string API keys** and **hardcoded keys in client code** are common, high-value exposure points to check during recon.
- Always test **missing, malformed, and expired tokens** explicitly — "fail open" authentication bugs are a critical, surprisingly common finding.
- API login/token endpoints deserve the same brute-force and rate-limit testing as web login forms — they're often less protected.

---
*Related: [[Broken_Authentication_and_Session_Management]], [[JWT_Attacks_and_API_Token_Security]], [[Unrestricted_Resource_Consumption_and_Rate_Limiting]]*
