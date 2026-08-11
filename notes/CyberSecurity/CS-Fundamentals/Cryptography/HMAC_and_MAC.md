## 1. Introduction

A **MAC (Message Authentication Code)** is a short piece of data derived from a message and a **secret key**, used to verify both the **integrity** and **authenticity** of a message — proving it wasn't altered and came from someone who possesses the shared secret key. This is a critical distinction from plain hashing: a MAC requires a secret key, while a plain hash does not.

## 2. Why Plain Hashing Isn't Enough for Authentication

A plain hash (e.g., `SHA-256(message)`) proves integrity only if the hash itself is transmitted through a trusted, tamper-proof channel — but if an attacker can intercept both the message and its hash, they can simply modify the message and recompute a matching hash. A MAC solves this because computing a valid MAC **requires the secret key**, which an attacker doesn't have.

```
Hash:  H(message)                    → anyone can recompute this
MAC:   H(message, secret_key)        → only someone with the key can recompute this
```

## 3. How MAC Verification Works

1. Sender computes `MAC = H(message, key)` and sends `message + MAC`.
2. Receiver, who shares the same secret key, recomputes `MAC' = H(message, key)` on the received message.
3. If `MAC == MAC'`, the message is authentic and unaltered. If they differ, the message was tampered with or the sender didn't have the correct key.

## 4. HMAC (Hash-based Message Authentication Code)

**HMAC** is the standard, widely-used construction for building a MAC out of a cryptographic hash function, defined in RFC 2104. It's specifically designed to be secure even when built from hash functions that have known weaknesses against length-extension attacks (see [[Hashing]]).

**Simplified construction:**

```
HMAC(key, message) = H( (key XOR opad) || H( (key XOR ipad) || message ) )
```

Where `ipad`/`opad` are fixed padding constants, and `||` denotes concatenation. This nested double-hashing structure is specifically what protects HMAC from length-extension attacks that affect naive `H(key || message)` constructions.

**Named variants:** HMAC-SHA256, HMAC-SHA1 (legacy), HMAC-MD5 (legacy) — named after the underlying hash function used.

## 5. Naive MAC Construction Pitfalls

| Naive Approach | Problem |
|---|---|
| `H(key \|\| message)` | Vulnerable to length-extension attacks on hash functions like MD5/SHA-1/SHA-256 |
| `H(message \|\| key)` | Vulnerable to certain collision-based attacks depending on the hash |
| Using a fast, unauthenticated hash alone (no key) | Provides no authentication at all — anyone can forge it |

**HMAC's specific construction avoids these issues** — this is why it's the standard recommendation over rolling your own key+hash combination.

## 6. CMAC and GMAC (Cipher-Based MACs)

MACs don't have to be hash-based — they can also be built from block ciphers:

| Type | Description |
|---|---|
| CMAC (Cipher-based MAC) | Built from a block cipher (e.g., AES-CMAC) rather than a hash function |
| GMAC | The authentication component of **GCM** (Galois/Counter Mode), used in AES-GCM (see [[Block_Cipher_Modes]]) |

## 7. MAC vs Digital Signature

| Aspect | MAC (e.g., HMAC) | Digital Signature |
|---|---|---|
| Key type | Symmetric (shared secret) | Asymmetric (private key to sign, public key to verify) |
| Non-repudiation | No — either party could have created it, since both hold the shared key | Yes — only the private key holder could have signed it |
| Speed | Fast | Slower (asymmetric operations) |
| Use case | Fast integrity/authenticity between two trusted parties sharing a key | Proving authorship to third parties who don't share a secret |

This distinction is a very common interview trap: a MAC proves the message came from *someone with the key* (which could be either party), while a digital signature proves it came from *one specific party* (the private key holder) — enabling non-repudiation.

## 8. AEAD — Authenticated Encryption with Associated Data

Modern systems combine encryption and authentication into a single operation for both efficiency and security, called **AEAD**:

| Scheme | Description |
|---|---|
| AES-GCM | AES in Galois/Counter Mode — provides both encryption and a built-in authentication tag (GMAC) |
| ChaCha20-Poly1305 | ChaCha20 stream cipher combined with the Poly1305 MAC |

**Encrypt-then-MAC (EtM)** is the recommended pattern when combining encryption and MAC manually: encrypt the plaintext first, then compute the MAC over the ciphertext. This avoids subtle vulnerabilities present in "MAC-then-Encrypt" or "Encrypt-and-MAC" orderings, notably **padding oracle attacks** (see [[Cryptographic_Attacks]]).

## 9. Real-World Use Cases

- **TLS** — record integrity is protected via MAC (older cipher suites) or AEAD (modern AES-GCM/ChaCha20-Poly1305 cipher suites).
- **API authentication** — HMAC-signed request signatures (e.g., AWS Signature v4, webhook payload verification like Stripe/GitHub webhooks).
- **JWT (JSON Web Tokens)** — the `HS256` algorithm uses HMAC-SHA256 to sign tokens with a shared secret.
- **IPSec** — uses HMAC (e.g., HMAC-SHA256) within ESP for packet authentication.

## 10. Interview Questions

1. What's the key difference between a hash and a MAC? → **A MAC requires a secret key; a plain hash does not**
2. What does HMAC stand for and why is it preferred over naive key+hash concatenation? → **Hash-based Message Authentication Code; its nested construction resists length-extension attacks that affect naive approaches**
3. What's the main difference between a MAC and a digital signature? → **MAC uses a symmetric shared key (no non-repudiation); digital signature uses asymmetric keys (provides non-repudiation)**
4. What does AEAD provide? → **Both confidentiality (encryption) and authenticity/integrity (MAC) in one combined operation**
5. What is Encrypt-then-MAC and why is it recommended? → **Encrypting first, then computing the MAC over the ciphertext — avoids padding oracle and other vulnerabilities from other orderings**
6. Give an example of HMAC used in a real protocol. → **JWT HS256 signing, AWS API request signing, TLS record authentication**

## 11. Key Points

- A MAC proves both **integrity** and **authenticity** using a **shared secret key** — unlike a plain hash.
- **HMAC** is the standard, secure construction for building a MAC from a hash function.
- MACs don't provide non-repudiation (both parties hold the key) — that requires **digital signatures**.
- **AEAD** (AES-GCM, ChaCha20-Poly1305) combines encryption and authentication in modern protocols.
- **Encrypt-then-MAC** is the recommended order when combining encryption and MAC manually.

---
*Related: [[Hashing]], [[Digital_Signatures]], [[Symmetric_Encryption]], [[Block_Cipher_Modes]]*
