## 1. Introduction

**Cryptography** is the science of securing information by transforming it so that only authorized parties can understand it. It underpins nearly every security control in modern computing — HTTPS, password storage, VPNs, digital signatures, cryptocurrencies. This note lays the foundational terminology and concepts that every other cryptography note in this folder builds on.

## 2. Core Goals of Cryptography

Cryptography aims to provide some combination of the following properties, often abbreviated **CIA + NA**:

| Goal | Description | Achieved via |
|---|---|---|
| Confidentiality | Only authorized parties can read the data | Encryption |
| Integrity | Data hasn't been altered | Hashing, MACs, digital signatures |
| Authentication | Verifying identity of a party | Digital signatures, certificates, MACs |
| Non-repudiation | Sender cannot deny having sent a message | Digital signatures |
| Availability | (Supported indirectly — crypto doesn't guarantee this on its own) | — |

## 3. Key Terminology

| Term | Meaning |
|---|---|
| Plaintext | The original, readable message |
| Ciphertext | The encrypted, unreadable form of the message |
| Cipher | The algorithm used to encrypt/decrypt |
| Key | A secret value that controls the cipher's output |
| Encryption | Converting plaintext → ciphertext |
| Decryption | Converting ciphertext → plaintext |
| Cryptanalysis | The study of breaking cryptographic systems |
| Cryptology | The umbrella field covering both cryptography and cryptanalysis |
| Keyspace | The total number of possible keys for a given algorithm |
| Cipher suite | A negotiated combination of algorithms (key exchange, cipher, MAC) used in a protocol like TLS |

## 4. Kerckhoffs's Principle

A cryptographic system should be **secure even if everything about the system, except the key, is public knowledge**. Security should rest entirely on the secrecy of the **key**, not on the secrecy of the algorithm ("security through obscurity" is not real security).

This is why modern algorithms (AES, RSA, SHA-256) are openly published, peer-reviewed, and standardized — their strength comes from mathematical hardness and key secrecy, not from hiding how they work.

## 5. Types of Cryptography (High-Level Map)

| Type | Description | Examples |
|---|---|---|
| Symmetric | Same key for encryption and decryption | AES, DES, 3DES (see [[Symmetric_Encryption]]) |
| Asymmetric | Different keys — public and private | RSA, ECC (see [[Asymmetric_Encryption]]) |
| Hashing | One-way transformation, no key needed to compute (or a key for MACs) | SHA-256, MD5 (see [[Hashing]]) |
| Hybrid | Combines symmetric + asymmetric for practical performance | TLS (see [[TLS_SSL]]) |

## 6. Substitution vs Transposition (Classical Ciphers)

Modern ciphers are built on two ancient core techniques:

| Technique | Description | Example |
|---|---|---|
| Substitution | Replaces each symbol with another | Caesar Cipher (shift letters by N), monoalphabetic/polyalphabetic ciphers |
| Transposition | Rearranges the order of symbols without changing them | Rail Fence Cipher, columnar transposition |

**Caesar Cipher example (shift = 3):** `HELLO` → `KHOOR`

**Vigenère Cipher:** a polyalphabetic substitution using a repeating keyword to vary the shift per letter — historically considered unbreakable ("le chiffre indéchiffrable") until Kasiski's frequency analysis broke it in the 19th century.

Modern block ciphers like AES actually combine both substitution (S-boxes) and permutation/transposition in rounds — this is called a **Substitution-Permutation Network (SPN)**.

## 7. Encryption, Encoding, and Hashing — Not the Same Thing

A very common point of confusion (see [[Encoding_vs_Encryption_vs_Hashing]] for the full breakdown):

| Operation | Reversible? | Needs a Key? | Purpose |
|---|---|---|---|
| Encoding (Base64, URL encoding) | Yes, always | No | Data format compatibility, not security |
| Encryption | Yes, with the correct key | Yes | Confidentiality |
| Hashing | No (one-way) | No (usually) | Integrity, fingerprinting, password storage |

## 8. Symmetric vs Asymmetric — At a Glance

| Aspect | Symmetric | Asymmetric |
|---|---|---|
| Keys | One shared secret key | Public + private key pair |
| Speed | Fast | Slow (computationally expensive) |
| Key distribution problem | Hard — must share key securely | Easy — public key can be shared openly |
| Typical use | Bulk data encryption | Key exchange, digital signatures, identity |
| Examples | AES, ChaCha20, 3DES | RSA, ECC, DSA |

In practice, real systems (like TLS) use **hybrid cryptography**: asymmetric crypto to securely exchange a symmetric session key, then symmetric crypto to encrypt the actual bulk data — combining the security of asymmetric key exchange with the speed of symmetric encryption.

## 9. Cryptographic Randomness

Strong cryptography depends on unpredictable, high-entropy randomness for keys, initialization vectors (IVs), and nonces.

- **CSPRNG (Cryptographically Secure Pseudo-Random Number Generator)** — required for anything security-sensitive (e.g., `/dev/urandom`, `CryptGenRandom`).
- Using a weak or predictable random number generator (or reusing a "random" value like an IV/nonce) has caused real-world breaks — e.g., reused nonces in stream ciphers can fully expose plaintext.

## 10. Interview Questions

1. What is the difference between cryptography and cryptanalysis? → **Cryptography secures information; cryptanalysis studies how to break that security**
2. What does Kerckhoffs's Principle state? → **A system should remain secure even if everything except the key is public**
3. Name the two classical cipher techniques modern ciphers build on. → **Substitution and transposition**
4. What's the core difference between encoding and encryption? → **Encoding needs no key and isn't meant for security; encryption requires a key and provides confidentiality**
5. Why do real systems use hybrid cryptography? → **To combine the secure key exchange of asymmetric crypto with the speed of symmetric crypto**
6. Why is a weak random number generator dangerous in cryptography? → **Predictable keys/IVs/nonces can be guessed or reused, breaking confidentiality**

## 11. Key Points

- Cryptography's core goals: **Confidentiality, Integrity, Authentication, Non-repudiation**.
- Security should depend only on the **key**, never on hiding the algorithm (Kerckhoffs's Principle).
- Three main categories: **Symmetric**, **Asymmetric**, **Hashing** — each solves a different problem.
- Encoding ≠ Encryption ≠ Hashing — reversibility and key requirements differ completely.
- Real-world systems combine symmetric and asymmetric crypto (hybrid) for both security and performance.
- Strong randomness (CSPRNGs) is a silent but critical requirement underlying all of cryptography.

---
*Related: [[Symmetric_Encryption]], [[Asymmetric_Encryption]], [[Hashing]], [[Encoding_vs_Encryption_vs_Hashing]]*
