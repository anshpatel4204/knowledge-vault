## 1. Introduction

A **digital signature** is a cryptographic mechanism that proves a message or document was created by a specific party and hasn't been altered since — it provides **authentication**, **integrity**, and, uniquely among cryptographic tools, **non-repudiation** (the signer cannot credibly deny having signed it). Digital signatures use **asymmetric cryptography**, but in the reverse role from encryption.

## 2. How Digital Signatures Work

```
Signing (sender, uses PRIVATE key):
  1. Hash the message → digest
  2. Encrypt/sign the digest with the sender's PRIVATE key → signature
  3. Send: message + signature

Verifying (receiver, uses sender's PUBLIC key):
  1. Hash the received message → digest'
  2. Decrypt the signature using the sender's PUBLIC key → digest
  3. Compare digest' to digest — if they match, the signature is valid
```

Only the private key holder could have produced a signature that the corresponding public key successfully verifies — this is the basis of authentication and non-repudiation.

## 3. Why Sign the Hash, Not the Whole Message

Asymmetric operations are computationally expensive. Instead of signing an entire (potentially huge) message directly, systems **hash the message first** (see [[Hashing]]) and sign only the small, fixed-size digest — dramatically improving performance while preserving security, since any change to the message changes its hash.

## 4. Digital Signature vs Encryption — Key Distinction

| Aspect | Encryption | Digital Signature |
|---|---|---|
| Purpose | Confidentiality | Authentication, integrity, non-repudiation |
| Key used to "lock" | Recipient's public key | Sender's private key |
| Key used to "unlock" | Recipient's private key | Sender's public key |
| Who can perform | Anyone (has public key) can encrypt | Only the private key holder can sign |

This is a common point of confusion: encryption uses the **recipient's** public key; signing uses the **sender's** private key — opposite roles for the same key-pair concept.

## 5. Common Signature Algorithms

| Algorithm | Basis | Notes |
|---|---|---|
| RSA Signatures (RSA-PSS, PKCS#1 v1.5) | Integer factorization | Widely deployed, larger signatures |
| DSA (Digital Signature Algorithm) | Discrete logarithm problem | NIST standard, largely superseded by ECDSA |
| ECDSA | Elliptic curve discrete logarithm | Smaller keys/signatures than RSA at equivalent security, widely used (TLS, Bitcoin) |
| EdDSA (Ed25519) | Elliptic curve (Edwards curves) | Modern, fast, deterministic (no reliance on a fresh random nonce per signature, avoiding a class of real-world implementation bugs) |

## 6. Non-Repudiation Explained

**Non-repudiation** means the signer cannot plausibly deny having signed a message, because signature creation requires the private key that only they possess. This differs from a **MAC** (see [[HMAC_and_MAC]]), where because both parties share the same secret key, either party could have created the MAC — offering no way to prove which one actually did.

| Mechanism | Non-Repudiation? |
|---|---|
| MAC / HMAC | No (shared key, either party could have made it) |
| Digital Signature | Yes (only the private key holder could have made it) |

## 7. Digital Certificates and Signatures

Digital signatures are the foundation of **PKI (Public Key Infrastructure)** (see [[PKI]]): a Certificate Authority (CA) digitally signs a certificate binding a public key to an identity. Anyone can verify that signature using the CA's public key, establishing a **chain of trust** without needing to independently verify every entity.

## 8. Signature Forgery Risks & Best Practices

| Risk | Mitigation |
|---|---|
| Weak/reused nonce (in DSA/ECDSA) | Can fully expose the private key (famously caused the 2010 Sony PS3 private key leak) — use deterministic nonce generation (RFC 6979) or EdDSA, which is deterministic by design |
| Signing raw data without hashing first | Slower and can expose subtle vulnerabilities — always hash-then-sign |
| Using a broken hash function (MD5/SHA-1) within the signature scheme | Attackers could forge a different message with the same signature — always pair signatures with a strong hash (SHA-256+) |
| Key compromise | Private key leakage invalidates all past and future signatures made with it — requires certificate revocation (CRL/OCSP) |

## 9. Real-World Use Cases

- **Code signing** — verifying software/drivers/updates haven't been tampered with (Windows Authenticode, Apple notarization).
- **TLS/HTTPS certificates** — CAs sign server certificates; browsers verify against trusted root CAs.
- **Email (S/MIME, PGP)** — signing emails to prove sender authenticity.
- **Document signing** — legally binding e-signature platforms (Adobe Sign, DocuSign) rely on digital signature cryptography underneath.
- **Blockchain/cryptocurrency** — transactions are signed with the sender's private key (typically ECDSA on the secp256k1 curve for Bitcoin) to prove authorization to spend funds.
- **JWT (RS256/ES256 algorithms)** — tokens signed with RSA or ECDSA for scenarios needing verifiable, non-shared-secret authentication (unlike HS256's HMAC).

## 10. Interview Questions

1. What three properties does a digital signature provide that a MAC does not fully provide? → **Authentication, integrity, and non-repudiation (specifically non-repudiation is the key differentiator)**
2. Which key is used to create a signature, and which to verify it? → **Private key to sign, public key to verify**
3. Why is the message hashed before signing? → **Hashing produces a small, fixed-size digest, making the expensive asymmetric signing operation fast and consistent regardless of message size**
4. Why doesn't a MAC provide non-repudiation? → **Because both parties share the same secret key, either could have generated it**
5. What real-world incident resulted from reused ECDSA nonces? → **The Sony PS3 private signing key was recovered because of a reused/static nonce**
6. Name a modern signature algorithm that avoids nonce-reuse vulnerabilities by design. → **EdDSA (Ed25519)**

## 11. Key Points

- Digital signatures use **asymmetric crypto in reverse**: sign with private key, verify with public key.
- Provide **authentication + integrity + non-repudiation** — the last being the key advantage over a MAC.
- Always **hash-then-sign** for performance and security.
- Common algorithms: **RSA, DSA, ECDSA, EdDSA (Ed25519)**.
- Nonce reuse in DSA/ECDSA can leak the private key entirely — EdDSA avoids this by being deterministic.
- Foundation of PKI, TLS certificates, code signing, and blockchain transaction authorization.

---
*Related: [[Asymmetric_Encryption]], [[Hashing]], [[HMAC_and_MAC]], [[PKI]]*
