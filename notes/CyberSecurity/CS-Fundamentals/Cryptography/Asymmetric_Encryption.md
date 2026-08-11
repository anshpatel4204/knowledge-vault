## 1. Introduction

**Asymmetric encryption** (also called **public-key cryptography**) uses a **mathematically linked key pair** — a **public key** (shared openly) and a **private key** (kept secret). Data encrypted with one key can only be decrypted with the other. This solves symmetric encryption's key distribution problem, at the cost of significantly more computational overhead.

## 2. How It Works

```
Encrypt with recipient's Public Key → only recipient's Private Key can decrypt   (Confidentiality)
Encrypt/Sign with sender's Private Key → anyone with sender's Public Key can verify   (Authentication / Signatures)
```

The two keys are mathematically related but computationally infeasible to derive one from the other — security rests on hard mathematical problems (factoring large primes, discrete logarithms, elliptic curve discrete logarithms).

## 3. Key Pair Roles

| Key | Kept | Used For |
|---|---|---|
| Public Key | Shared with everyone | Encrypting data for the owner; verifying the owner's signatures |
| Private Key | Kept secret by the owner | Decrypting data sent to them; creating signatures |

## 4. RSA (Rivest-Shamir-Adleman)

The most widely deployed asymmetric algorithm, based on the difficulty of **factoring the product of two large prime numbers**.

- **Key generation:** pick two large primes `p` and `q`, compute `n = p × q` (the modulus). Public/private keys are derived from `n` and Euler's totient function.
- **Key sizes:** 2048-bit (current minimum standard), 3072-bit or 4096-bit for higher security margins. 1024-bit is now considered weak.
- **Security basis:** factoring a 2048-bit+ number into its prime components is computationally infeasible with classical computers.
- **Use cases:** key exchange, digital signatures, certificate signing (still very common in PKI/TLS, though being gradually supplemented by ECC).

## 5. ECC (Elliptic Curve Cryptography)

Based on the algebraic structure of elliptic curves over finite fields — provides **equivalent security to RSA at much smaller key sizes**, making it faster and more efficient (important for mobile/IoT).

| RSA Key Size | Equivalent ECC Key Size |
|---|---|
| 2048-bit | ~224-bit |
| 3072-bit | ~256-bit |
| 15360-bit | ~512-bit |

**Common curves:** P-256 (secp256r1), Curve25519 (used in modern protocols like WireGuard and Signal), P-384.

**ECDSA / EdDSA** — elliptic-curve variants of digital signature algorithms, increasingly preferred over RSA signatures for performance.

## 6. Diffie-Hellman (Brief — see [[Key_Exchange]] for full detail)

Diffie-Hellman is technically a **key exchange protocol**, not an encryption algorithm — it allows two parties to derive a shared secret over an insecure channel without ever transmitting the secret itself, based on the discrete logarithm problem.

## 7. RSA vs ECC — Quick Comparison

| Aspect | RSA | ECC |
|---|---|---|
| Hard problem | Integer factorization | Elliptic curve discrete logarithm |
| Key size for equivalent security | Larger | Much smaller |
| Speed | Slower | Faster, especially key generation |
| Adoption | Extremely widespread, legacy-compatible | Growing rapidly, modern default in many protocols |

## 8. Why Asymmetric Crypto Isn't Used for Bulk Data

Asymmetric algorithms are **orders of magnitude slower** than symmetric ones due to the complex math involved (modular exponentiation, elliptic curve point multiplication). In practice, asymmetric crypto is used to securely exchange a **symmetric session key**, and the bulk data is then encrypted with fast symmetric algorithms like AES — this is **hybrid encryption**, the model used by TLS, PGP, and most secure messaging apps.

## 9. Digital Signatures (Brief — see [[Digital_Signatures]] for full detail)

Asymmetric crypto also enables **digital signatures**: signing with a private key, verifying with the corresponding public key — providing authentication, integrity, and non-repudiation, the reverse-direction use of the same key pair concept.

## 10. Real-World Use Cases

- **TLS/HTTPS** — asymmetric crypto authenticates the server (via certificates) and negotiates a symmetric session key.
- **PGP/GPG** — email encryption and signing.
- **SSH** — public-key authentication for secure remote login.
- **Digital certificates / PKI** — CAs sign certificates using their private key; anyone can verify with the CA's public key (see [[PKI]]).
- **Cryptocurrencies** — wallets are essentially key pairs; transactions are signed with the private key.

## 11. Quantum Computing Threat

Both RSA and ECC rely on mathematical problems that a sufficiently powerful **quantum computer** could solve efficiently using **Shor's Algorithm**, which would break them. This has driven active research and standardization of **Post-Quantum Cryptography (PQC)** — algorithms based on different hard problems (lattice-based, hash-based, code-based) believed resistant to quantum attacks. NIST finalized its first PQC standards (e.g., CRYSTALS-Kyber for key exchange, CRYSTALS-Dilithium for signatures) in recent years.

## 12. Interview Questions

1. What problem does asymmetric encryption solve that symmetric encryption doesn't? → **Secure key distribution without a pre-shared secret**
2. What mathematical problem does RSA's security rely on? → **Factoring the product of two large primes**
3. Why is ECC preferred in modern, performance-sensitive systems? → **It achieves equivalent security to RSA with much smaller key sizes, making it faster**
4. Why isn't asymmetric encryption used to encrypt large amounts of data directly? → **It's computationally much slower than symmetric encryption**
5. What is hybrid encryption? → **Using asymmetric crypto to exchange a symmetric session key, then symmetric crypto for the actual data**
6. What threat does post-quantum cryptography defend against? → **Quantum computers using Shor's Algorithm to break RSA/ECC**

## 13. Key Points

- Asymmetric crypto uses a **public/private key pair** — mathematically linked but computationally infeasible to derive one from the other.
- **RSA** (factoring-based) and **ECC** (elliptic curve-based) are the dominant algorithms; ECC offers smaller keys for equivalent security.
- Public key encrypts/verifies; private key decrypts/signs.
- Too slow for bulk data — real systems use **hybrid encryption** (asymmetric for key exchange, symmetric for data).
- Quantum computing threatens both RSA and ECC long-term, driving adoption of **Post-Quantum Cryptography**.

---
*Related: [[Cryptography_Basics]], [[Symmetric_Encryption]], [[Key_Exchange]], [[Digital_Signatures]], [[PKI]]*
