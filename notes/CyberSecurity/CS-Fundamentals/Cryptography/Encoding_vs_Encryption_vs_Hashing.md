## 1. Introduction

Encoding, encryption, and hashing are three fundamentally different data transformations that get confused constantly — including by working professionals and in interviews. Mixing them up leads to real security bugs (e.g., "encoding" a password with Base64 and calling it "encrypted"). This note draws the line clearly between all three, plus a fourth related concept: obfuscation.

## 2. The Core Distinction

| Operation | Reversible? | Needs a Key? | Purpose | Security Property |
|---|---|---|---|---|
| Encoding | Yes, always, by design | No | Data format compatibility/transportability | None — provides zero confidentiality |
| Encryption | Yes, with the correct key | Yes | Confidentiality | Strong, if algorithm/key/implementation are sound |
| Hashing | No (one-way) | No (usually) | Integrity, fingerprinting, password storage | Provides integrity/verification, not confidentiality |
| Obfuscation | Often, with effort | No (by design) | Make something harder to understand/read | Weak — "security through obscurity" |

## 3. Encoding

**Encoding** transforms data into a different format for **compatibility** reasons — not security. Anyone can reverse it instantly using the same publicly known algorithm, with no key involved at all.

**Common encoding schemes:**

| Scheme | Purpose |
|---|---|
| Base64 | Represents binary data as ASCII text (e.g., for embedding images in HTML/JSON, email attachments) |
| URL Encoding (percent-encoding) | Makes special characters safe for use in URLs |
| Hex Encoding | Represents binary data as hexadecimal digits |
| ASCII / UTF-8 | Character encoding standards for representing text as bytes |

**Common mistake:** Base64-"encoding" a password and treating it as if it were encrypted. It is trivially reversible by anyone — `atob()` in a browser console decodes it instantly. **Base64 is not encryption.**

```
Base64("password123") = cGFzc3dvcmQxMjM=
Base64.decode("cGFzc3dvcmQxMjM=") = "password123"   ← trivial to reverse, no key needed
```

## 4. Encryption

**Encryption** transforms data using an algorithm **and a secret key**, producing ciphertext that can only be reversed (decrypted) by someone who possesses the correct key. See [[Symmetric_Encryption]] and [[Asymmetric_Encryption]] for full detail.

```
Encrypt(plaintext, key) → ciphertext
Decrypt(ciphertext, key) → plaintext        (only works with the correct key)
```

Provides genuine **confidentiality** — without the key, recovering the plaintext should be computationally infeasible.

## 5. Hashing

**Hashing** transforms input of any size into a fixed-size output (digest) via a **one-way function** — there is no key involved in plain hashing, and it cannot be reversed by design (see [[Hashing]]).

```
Hash(input) → fixed-size digest    (cannot be reversed back to input)
```

Used for **integrity verification** and **fingerprinting**, not confidentiality. A hash of a password lets you *verify* a password attempt matches without ever storing the original password.

## 6. Obfuscation

**Obfuscation** makes data or code deliberately harder to read/understand — but doesn't rely on mathematical hardness, just complexity/confusion. Common in software (minified/obfuscated JavaScript, packed malware) to slow down analysis, not to provide cryptographic guarantees.

**Key weakness:** obfuscation is "security through obscurity" — determined attackers with enough time/tools (deobfuscators, debuggers, decompilers) can typically reverse it. It should never be relied upon as the sole protection for genuinely sensitive data.

## 7. Side-by-Side Example

Take the string `"SecretData"`:

| Operation | Result | Can you get back "SecretData"? |
|---|---|---|
| Base64 Encode | `U2VjcmV0RGF0YQ==` | Yes, instantly, no key needed |
| AES Encrypt (key=`mykey123`) | `8f3a2b91e4...` (unreadable binary) | Yes, but only with the correct key |
| SHA-256 Hash | `a591a6d40bf...` | **No — one-way, never reversible** |

## 8. Decision Table — Which One Do You Need?

| Goal | Use |
|---|---|
| Store a password securely | Hashing (with salt, using bcrypt/Argon2/scrypt) |
| Transmit binary data safely inside JSON/text/URLs | Encoding (Base64/URL encoding) |
| Protect the confidentiality of stored/transmitted data | Encryption (AES, RSA, etc.) |
| Verify a file hasn't been tampered with | Hashing (SHA-256 checksum) |
| Verify a message's authenticity + integrity together | MAC/HMAC (see [[HMAC_and_MAC]]) |
| Prove non-repudiable authorship | Digital signature (see [[Digital_Signatures]]) |
| Slow down casual reverse-engineering of code (not a security guarantee) | Obfuscation |

## 9. Common Real-World Mistakes

- Storing passwords with reversible encryption (or worse, Base64/encoding) instead of a proper slow hash — if the encryption key or encoding scheme is ever known, all passwords are instantly exposed.
- Calling Base64 "encryption" in documentation/marketing — misleads stakeholders about actual security posture.
- Using a fast general-purpose hash (plain SHA-256) for passwords instead of Argon2/bcrypt — makes brute-forcing feasible at scale.
- Relying purely on code obfuscation to protect embedded secrets (API keys, hardcoded credentials) instead of removing them entirely or using proper secret management.

## 10. Interview Questions

1. Is Base64 a form of encryption? → **No — it's encoding, requires no key, and is trivially reversible by anyone**
2. What's the key difference between encryption and hashing? → **Encryption is reversible with a key (confidentiality); hashing is one-way and irreversible (integrity/fingerprinting)**
3. Why shouldn't you store passwords using encryption instead of hashing? → **If the encryption key is compromised, all passwords become instantly recoverable — a properly salted, slow hash doesn't have this single point of failure**
4. What's the weakness of relying on obfuscation alone? → **It's security through obscurity — determined attackers can eventually reverse it with enough effort/tools**
5. Give an example of encoding used correctly (not as a security control). → **Base64-encoding a binary image to embed it inside a JSON API response**
6. What would you use to both encrypt data and verify it hasn't been tampered with in one step? → **An AEAD cipher mode like AES-GCM (see [[Block_Cipher_Modes]])**

## 11. Key Points

- **Encoding** ≠ security — no key, always reversible, purely for compatibility (Base64, URL encoding).
- **Encryption** = confidentiality — reversible only with the correct key.
- **Hashing** = integrity/fingerprinting — one-way, never reversible, no key (in plain form).
- **Obfuscation** = weak, effort-based deterrence, not a cryptographic guarantee.
- The most common real-world mistake: confusing encoding (or obfuscation) with actual encryption when protecting sensitive data.

---
*Related: [[Cryptography_Basics]], [[Hashing]], [[Symmetric_Encryption]], [[HMAC_and_MAC]]*
