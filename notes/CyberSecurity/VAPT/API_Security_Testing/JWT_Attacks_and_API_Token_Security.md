## 1. Introduction

**JSON Web Tokens (JWTs)** are the dominant token format for modern API authentication, self-contained enough to carry identity and permission claims without a server-side session lookup — which is exactly what makes a broken JWT implementation so severe: forging or tampering with one can forge identity and permissions directly.

## 2. JWT Structure

```
header.payload.signature

# Example (decoded):
Header:  {"alg": "HS256", "typ": "JWT"}
Payload: {"sub": "1042", "role": "user", "exp": 1750000000}
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

The header and payload are only **Base64-encoded, not encrypted** — anyone can read (and modify) their contents; the signature is what's supposed to prevent tampering from going undetected.

## 3. The "alg: none" Attack

Some JWT libraries historically honored an `alg` of `none`, meaning no signature verification occurs at all.

```
1. Decode the JWT, change "alg": "HS256" to "alg": "none"
2. Modify the payload freely (e.g. "role": "user" → "role": "admin")
3. Submit the token with an EMPTY signature section: header.payload.
4. If the server accepts it, authentication is fully bypassed
```

## 4. Algorithm Confusion (RS256 → HS256)

A more subtle and still-relevant attack against servers using asymmetric signing (RS256, a public/private key pair):

```
1. Obtain the server's PUBLIC key (often exposed at a /.well-known/jwks.json endpoint, or reused from elsewhere)
2. Craft a token with "alg": "HS256" instead
3. Sign it using the server's public key AS THE HMAC SECRET
4. A server that doesn't strictly enforce which algorithm it expects may verify
   the token using the public key as an HMAC secret — a check that trivially passes,
   since the attacker generated the signature the same way
```

## 5. Weak Secret Brute-Forcing

For HS256 (symmetric) tokens, if the signing secret is weak or a common default, it can be cracked offline:

```
hashcat -a 0 -m 16500 jwt.txt rockyou.txt
```

Once the secret is recovered, an attacker can forge arbitrary valid tokens with any claims they want.

## 6. Other Common JWT Issues

| Issue | Risk |
|---|---|
| No expiration (`exp`) claim, or not enforced | Stolen tokens remain valid indefinitely |
| No revocation mechanism | A compromised token can't be invalidated before its natural expiry, even after detection |
| Sensitive data in the payload | Since the payload is only Base64-encoded, anything placed there (PII, internal flags) is effectively public |
| `kid` (Key ID) header injection | Manipulating the `kid` header to point the server at an attacker-influenced key source (e.g. path traversal to a predictable file, or SQL injection into a key-lookup query) |

## 7. Interview Questions

1. Why is it a mistake to assume a JWT's payload is confidential? → **The payload is only Base64-encoded, not encrypted — anyone who obtains the token can decode and read every claim inside it directly**
2. Explain the "alg: none" JWT attack. → **Some implementations historically accepted a token whose header declares `"alg": "none"`, skipping signature verification entirely — an attacker can freely modify the payload's claims (e.g. escalate role to admin) and submit the token with an empty signature**
3. How does the RS256-to-HS256 algorithm confusion attack work? → **The attacker obtains the server's RS256 public key, then crafts and signs a token using HS256 with that public key as the HMAC secret — if the server doesn't strictly enforce the expected algorithm, it verifies the forged HS256 signature using the same public key, which trivially succeeds**
4. Why does recovering a weak HS256 signing secret compromise the entire token scheme, not just one token? → **Once the shared secret is known, an attacker can sign arbitrary tokens with any claims they choose, forging valid authentication for any user or role indefinitely — not just replaying one captured token**

## 8. Key Points

- JWT payloads are **encoded, not encrypted** — never assume confidentiality of claims inside a token.
- The **`alg: none`** attack and **RS256/HS256 algorithm confusion** both stem from a server trusting the token's own `alg` header instead of strictly enforcing an expected algorithm.
- Weak HS256 secrets are crackable offline via tools like **hashcat**, after which arbitrary tokens can be forged.
- Always check for **expiration enforcement**, a **revocation mechanism**, and sensitive data leakage in the payload as part of a full JWT assessment.

---
*Related: [[Broken_Authentication_in_APIs]], [[Broken_Authentication_and_Session_Management]], [[API_Security_Assessment_Workflow]]*
