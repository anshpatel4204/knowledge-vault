## 1. Introduction

Cryptographic attacks target weaknesses in algorithms, implementations, protocols, or key management rather than "brute forcing the math" outright. Understanding these attack classes is essential for both attackers assessing weak crypto and defenders choosing/configuring the right algorithms. This note catalogs the major categories referenced throughout this folder.

## 2. Brute Force Attack

Systematically trying every possible key until the correct one is found.

- **Feasibility depends entirely on key size** — 56-bit DES keys are brute-forceable in hours with modern hardware; 128/256-bit AES keys are not, even with massive computing resources.
- **Mitigation:** use sufficiently large key sizes (AES-128 minimum, AES-256 for long-term security; RSA-2048+/ECC-256+ for asymmetric).

## 3. Dictionary & Credential Attacks

| Attack | Description |
|---|---|
| Dictionary Attack | Trying a list of common passwords/words against a hash or login |
| Credential Stuffing | Using leaked username/password pairs from one breach against other services |
| Rainbow Table Attack | Using precomputed hash-to-plaintext tables to reverse unsalted hashes quickly |

**Mitigation:** salting (see [[Hashing]]), slow password hashing (bcrypt/Argon2), MFA, rate limiting/account lockout.

## 4. Birthday Attack

Exploits the **birthday paradox** — the surprising probability that two items share a property much sooner than intuition suggests. Applied to hashing: finding **any** two inputs that collide requires only about `2^(n/2)` attempts for an n-bit hash, not `2^n`.

- For a 128-bit hash, a collision is found in ~2^64 attempts, not 2^128 — this is why **hash output size matters more than it might seem**, and why MD5 (128-bit) and SHA-1 (160-bit) are considered too weak against dedicated attackers today.
- This is the mathematical basis behind real collision attacks against MD5 and SHA-1.

## 5. Man-in-the-Middle (MITM) Attack

An attacker secretly intercepts and potentially alters communication between two parties who believe they're communicating directly with each other.

- Plain Diffie-Hellman (see [[Key_Exchange]]) is vulnerable to MITM without authentication.
- TLS defeats MITM via certificate-based server authentication (see [[PKI]], [[TLS_SSL]]) — but only if the client actually validates the certificate chain properly (many real-world app vulnerabilities come from skipping this check).

## 6. Padding Oracle Attack

Exploits systems that reveal (even indirectly, e.g., via timing or distinguishable error messages) whether decrypted ciphertext has **valid padding** (e.g., PKCS#7 in CBC mode). By manipulating ciphertext and observing the oracle's padding-valid/invalid responses, an attacker can decrypt data byte-by-byte **without ever knowing the key**.

- Real-world example: **POODLE** attack against SSL 3.0.
- **Mitigation:** use AEAD modes like GCM (built-in authentication catches tampering before padding is even checked), or ensure padding errors and MAC errors return identical, constant-time responses.

## 7. Replay Attack

Capturing a valid, legitimate message/transmission and retransmitting it later to trigger unauthorized repeated actions (e.g., replaying a captured "transfer $100" authenticated request).

**Mitigation:** nonces, timestamps, sequence numbers, session tokens with short validity windows.

## 8. Side-Channel Attacks

Exploit information leaked through the **physical implementation** of a cryptosystem rather than a weakness in the algorithm itself.

| Type | Description |
|---|---|
| Timing Attack | Measuring how long an operation takes to infer secret data (e.g., non-constant-time comparison of a MAC leaking correct byte-by-byte guesses) |
| Power Analysis | Measuring power consumption of a device (e.g., smart card) during crypto operations to infer key bits |
| Electromagnetic Analysis | Capturing EM emissions from a device during computation |
| Acoustic Cryptanalysis | Inferring key material from sounds a device makes during computation |
| Cache-Timing Attacks | Exploiting CPU cache access patterns (e.g., early non-constant-time AES table lookup implementations) |

**Mitigation:** constant-time algorithm implementations, hardware security modules (HSMs) with side-channel protections, masking/blinding techniques.

## 9. Chosen-Plaintext / Chosen-Ciphertext Attacks

| Attack | Attacker Capability |
|---|---|
| Known-Plaintext Attack (KPA) | Has some plaintext/ciphertext pairs, tries to deduce the key |
| Chosen-Plaintext Attack (CPA) | Can choose arbitrary plaintexts and see their ciphertexts |
| Chosen-Ciphertext Attack (CCA) | Can choose arbitrary ciphertexts and see their decrypted plaintexts (e.g., via an oracle) |

Modern algorithms/modes (like AES-GCM) are designed to be secure even against CCA — a key requirement in formal cryptographic proofs (IND-CCA security).

## 10. Downgrade Attacks

Forcing two parties to negotiate a weaker, older, more vulnerable protocol version or cipher suite than they'd otherwise use (e.g., forcing TLS 1.2 → SSL 3.0 to then exploit POODLE). Mitigated by protocols refusing to negotiate below a minimum acceptable version and using signed handshake transcripts to detect tampering (as TLS 1.3 does).

## 11. Length Extension Attack

Exploits the internal Merkle-Damgård structure of hash functions like MD5, SHA-1, and SHA-256 — allows an attacker who knows `H(secret || message)` (but not `secret`) to compute `H(secret || message || extension)` for an attacker-chosen extension, **without knowing the secret**. This is exactly why naive `H(key || message)` MAC constructions are unsafe, and why **HMAC** exists (see [[HMAC_and_MAC]]).

## 12. Key Management Failures (Often the Real Weak Point)

Most real-world cryptographic breaches come from **implementation and key management failures**, not from breaking the math:

- Hardcoded/embedded secret keys in source code or mobile apps.
- Weak or predictable random number generation for keys/IVs/nonces (e.g., Debian's 2008 OpenSSL PRNG bug drastically reduced SSH/TLS key entropy).
- Reusing IVs/nonces (catastrophic in CTR/GCM/stream ciphers).
- Failing to rotate keys or revoke compromised certificates.
- Storing keys unencrypted, or without proper access control/HSM protection.

## 13. Interview Questions

1. What does a birthday attack exploit? → **The birthday paradox — collisions are found in roughly 2^(n/2) attempts for an n-bit hash, not 2^n**
2. What does a padding oracle attack allow an attacker to do? → **Decrypt ciphertext byte-by-byte without the key, by exploiting padding-validity error signals**
3. What's the difference between a known-plaintext and chosen-plaintext attack? → **Known-plaintext: attacker has existing pairs; chosen-plaintext: attacker can choose arbitrary plaintexts to encrypt**
4. What real-world SSL 3.0 attack exploited CBC padding? → **POODLE**
5. Why are side-channel attacks dangerous even against mathematically strong algorithms? → **They exploit the physical implementation (timing, power, EM) rather than the algorithm's math**
6. What is the most common real-world cause of cryptographic failures? → **Implementation/key management mistakes (weak randomness, nonce reuse, hardcoded keys) rather than broken algorithms**

## 14. Key Points

- **Brute force** feasibility depends on key size; modern key sizes (AES-256, RSA-2048+) resist it.
- **Birthday attacks** make hash collisions feasible at roughly the square root of the naive difficulty — output size matters.
- **Padding oracle** and **length extension** attacks exploit specific implementation details — AEAD modes (GCM) and HMAC exist specifically to close these gaps.
- **Side-channel attacks** target the physical implementation, not the math — mitigated with constant-time code.
- The overwhelming majority of real-world crypto breaches stem from **key management and implementation mistakes**, not broken algorithms.

---
*Related: [[Hashing]], [[Block_Cipher_Modes]], [[HMAC_and_MAC]], [[Key_Exchange]], [[TLS_SSL]]*
