## 1. Introduction

**Elliptic Curve Cryptography (ECC)** is a form of public-key cryptography based on the algebraic structure of elliptic curves over finite fields. It delivers the **same security level as RSA with dramatically smaller keys**, making it faster, more efficient, and better suited to constrained environments (mobile, IoT, smart cards) — which is why it has become the modern default across TLS, SSH, cryptocurrencies, and secure messaging.

## 2. What Is an Elliptic Curve

An elliptic curve used in cryptography is defined by an equation of the form:

```
y² = x³ + ax + b   (mod p)
```

where `a`, `b`, and the prime modulus `p` are fixed public parameters defining the specific curve. The set of points `(x, y)` satisfying this equation, plus a special "point at infinity," form a mathematical group with a well-defined **point addition** operation.

## 3. The Hard Problem — ECDLP

ECC's security rests on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**: given a starting point `G` (the generator/base point) on the curve and a resulting point `P = k × G` (where `×` means "add G to itself k times," called scalar multiplication), it is computationally infeasible to determine `k` — even though computing `P` from `k` and `G` is easy.

This asymmetry (easy one direction, infeasible to reverse) is exactly the same shape of hard problem RSA relies on (factoring), just built on different math — and it turns out to be **harder to solve per bit of key size** than RSA's factoring problem, which is why ECC needs much smaller keys for equivalent security.

## 4. Key Generation

1. Both parties agree on public curve parameters (the curve equation, prime `p`, generator point `G`).
2. Private key: a randomly chosen large integer `k`.
3. Public key: the curve point `K = k × G`, computed via scalar multiplication.

Recovering `k` from `K` and `G` (i.e., breaking ECDLP) is the infeasible part attackers face.

## 5. Security Comparison — RSA vs ECC Key Sizes

| RSA Key Size | Equivalent ECC Key Size | Symmetric Equivalent |
|---|---|---|
| 1024-bit (weak, deprecated) | ~160-bit | 80-bit |
| 2048-bit | ~224-bit | 112-bit |
| 3072-bit | ~256-bit | 128-bit |
| 7680-bit | ~384-bit | 192-bit |
| 15360-bit | ~512-bit | 256-bit |

A 256-bit ECC key provides roughly the same security as a 3072-bit RSA key — over **10x smaller**, translating directly into faster computation, smaller certificates/signatures, and lower bandwidth/storage overhead.

## 6. Common Named Curves

| Curve | Notes |
|---|---|
| P-256 (secp256r1 / prime256v1) | NIST-standardized, extremely widely deployed (TLS default for years) |
| P-384, P-521 | Higher-security NIST curves |
| secp256k1 | Used by Bitcoin and Ethereum for transaction signing — notably NOT a NIST curve |
| Curve25519 | Designed by Daniel J. Bernstein, optimized for speed and resistance to implementation pitfalls, used for key exchange (X25519) in WireGuard, Signal, TLS 1.3 |
| Ed25519 | The EdDSA signature variant built on Curve25519 — deterministic (no per-signature random nonce needed), avoiding a whole class of nonce-reuse vulnerabilities that have plagued ECDSA implementations |

**Why Curve25519/Ed25519 gained popularity:** NIST curves faced scrutiny after 2013 revelations about potential backdooring concerns in a related NIST-standardized RNG (Dual_EC_DRBG — a separate but reputation-damaging incident); Bernstein's curves were designed with fully transparent, "nothing-up-my-sleeve" parameter choices and simpler, safer implementation properties.

## 7. ECDH (Elliptic Curve Diffie-Hellman)

The elliptic-curve variant of Diffie-Hellman key exchange (see [[Key_Exchange]] for the full mechanics) — both parties combine their private key with the other's public key via scalar multiplication to arrive at the same shared secret point, without ever transmitting private key material.

## 8. ECDSA and EdDSA (Signatures)

Elliptic-curve variants of digital signature algorithms (see [[Digital_Signatures]]):

| Algorithm | Notes |
|---|---|
| ECDSA | Requires a fresh, unpredictable random nonce per signature — nonce reuse or weak randomness catastrophically leaks the private key (this is exactly what happened in the 2010 Sony PlayStation 3 signing key leak) |
| EdDSA (Ed25519) | Deterministic — derives the nonce from the message and private key instead of external randomness, eliminating the nonce-reuse attack class entirely |

## 9. Advantages and Disadvantages of ECC

**Advantages:**

- Much smaller keys/signatures for equivalent security → faster computation, less bandwidth, smaller certificates.
- Well-suited for resource-constrained devices (mobile, IoT, smart cards).
- Modern curves (Curve25519/Ed25519) offer strong resistance to common implementation pitfalls.

**Disadvantages:**

- More mathematically complex to implement correctly than RSA — subtle implementation bugs (invalid curve attacks, non-constant-time operations, weak randomness) have historically caused real vulnerabilities.
- Slightly less universal legacy/hardware support than RSA, though this gap has closed significantly.
- Like RSA, vulnerable to future quantum computers via Shor's Algorithm (see [[Post_Quantum_Cryptography]]).

## 10. Real-World Use Cases

- **TLS 1.3** — ECDHE for key exchange, ECDSA/Ed25519 for certificate signatures.
- **SSH** — Ed25519 keys are now a common recommended default for user/host authentication.
- **Cryptocurrencies** — Bitcoin and Ethereum use secp256k1 ECDSA for transaction signing.
- **Signal Protocol / WhatsApp / secure messaging** — Curve25519 (X25519) for key exchange.
- **WireGuard VPN** — Curve25519 for key exchange, entirely built around modern ECC.
- **Mobile/IoT device authentication** — smaller keys make ECC practical where RSA would be too resource-intensive.

## 11. Interview Questions

1. What hard mathematical problem underlies ECC's security? → **The Elliptic Curve Discrete Logarithm Problem (ECDLP)**
2. Why does ECC use much smaller keys than RSA for equivalent security? → **ECDLP is harder to solve per bit than RSA's factoring problem, so smaller keys still resist attack**
3. What ECC key size is roughly equivalent to a 3072-bit RSA key? → **256-bit**
4. What's the key advantage of Ed25519 over standard ECDSA? → **It's deterministic — no external random nonce is needed per signature, eliminating nonce-reuse vulnerabilities**
5. Which elliptic curve does Bitcoin use? → **secp256k1**
6. Is ECC quantum-resistant? → **No — like RSA, it's vulnerable to Shor's Algorithm on a sufficiently powerful quantum computer**

## 12. Key Points

- ECC provides public-key cryptography with **much smaller keys** than RSA for the same security level, based on the ECDLP hard problem.
- Common curves: **P-256** (NIST/legacy default), **secp256k1** (Bitcoin), **Curve25519/Ed25519** (modern preferred default).
- **ECDH** handles key exchange; **ECDSA/EdDSA** handle digital signatures.
- **Ed25519 is deterministic**, avoiding the nonce-reuse vulnerabilities that have historically broken ECDSA implementations (e.g., the Sony PS3 key leak).
- Like RSA, ECC is **not quantum-resistant** — long-term systems are migrating toward post-quantum algorithms.

---
*Related: [[Asymmetric_Encryption]], [[Key_Exchange]], [[Digital_Signatures]], [[Post_Quantum_Cryptography]]*
