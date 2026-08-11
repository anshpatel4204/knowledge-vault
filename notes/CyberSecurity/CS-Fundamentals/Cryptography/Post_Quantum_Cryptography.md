## 1. Introduction

**Post-Quantum Cryptography (PQC)** is the field developing cryptographic algorithms designed to remain secure against attacks from **quantum computers** — which, once sufficiently powerful, could break the most widely deployed public-key algorithms in use today (RSA, ECC, Diffie-Hellman). This isn't a future-hypothetical topic for cryptographers: standardization has already happened, and migration is actively underway in major browsers, operating systems, and protocols.

## 2. Why Quantum Computers Threaten Current Cryptography

Classical computers solve problems like integer factorization (RSA) and discrete logarithms (DH, ECC) using algorithms whose runtime grows so fast with key size that sufficiently large keys are practically unbreakable. Quantum computers, however, can run fundamentally different algorithms that solve these *specific* problems exponentially faster.

### Shor's Algorithm
A quantum algorithm that can efficiently factor large integers and solve discrete logarithm problems — directly breaking:
- **RSA** (relies on factoring)
- **Diffie-Hellman / DSA** (relies on discrete logarithms)
- **ECC / ECDH / ECDSA** (relies on the elliptic curve discrete logarithm problem)

A sufficiently large, fault-tolerant quantum computer running Shor's Algorithm would render all of these **completely broken**, not just weakened.

### Grover's Algorithm
A quantum algorithm that provides a quadratic speedup for **brute-force search** problems — relevant to symmetric cryptography and hashing, but far less catastrophic:

| Algorithm Type | Classical Security | Quantum Security (via Grover's) |
|---|---|---|
| AES-128 | 128-bit | Effectively ~64-bit (weakened, not broken) |
| AES-256 | 256-bit | Effectively ~128-bit (still strong) |
| SHA-256 | 256-bit | Effectively ~128-bit (still strong) |

**Key takeaway:** symmetric algorithms and hash functions are **not broken** by quantum computers — they just need larger key/output sizes to maintain the same margin of safety (which is why AES-256 and SHA-384/512 are recommended for long-term "quantum-resistant" symmetric use, simply by having enough bits to spare).

## 3. Asymmetric Crypto Is the Real Casualty

| Algorithm | Classically Secure? | Quantum-Secure? |
|---|---|---|
| RSA | Yes (with adequate key size) | **No — fully broken by Shor's Algorithm** |
| Diffie-Hellman / ECDH | Yes | **No — fully broken** |
| DSA / ECDSA / EdDSA | Yes | **No — fully broken** |
| AES-256 | Yes | Yes (with Grover's margin accounted for) |
| SHA-256/SHA-3 | Yes | Yes (with Grover's margin accounted for) |

This asymmetry is exactly why PQC research focuses almost entirely on replacing **asymmetric** algorithms (key exchange and signatures) — symmetric crypto just needs bigger keys, which we already largely use.

## 4. "Harvest Now, Decrypt Later" — Why This Matters Today

Even though large-scale, cryptographically-relevant quantum computers don't exist yet, adversaries (particularly nation-states) are believed to already be **recording encrypted traffic today**, betting on decrypting it retroactively once quantum computers mature. Any data with long-term sensitivity (government, medical, financial records with decades-long confidentiality requirements) is at risk **right now**, even though the attack capability doesn't exist yet — which is why migration urgency is treated as immediate rather than something to defer.

## 5. NIST Post-Quantum Standardization

NIST ran a multi-year, multi-round public competition (starting 2016) to select standardized PQC algorithms. The finalized standards (published 2024) are based on different underlying hard mathematical problems believed resistant to both classical and quantum attack:

| Algorithm | Purpose | Underlying Problem | Standard Name |
|---|---|---|---|
| CRYSTALS-Kyber | Key Encapsulation (key exchange) | Lattice-based (Module-LWE) | ML-KEM |
| CRYSTALS-Dilithium | Digital Signatures | Lattice-based (Module-LWE/SIS) | ML-DSA |
| SPHINCS+ | Digital Signatures | Hash-based | SLH-DSA |
| FALCON | Digital Signatures | Lattice-based (NTRU) | FN-DSA |

## 6. Families of Post-Quantum Approaches

| Family | Basis | Notes |
|---|---|---|
| Lattice-based | Hardness of problems in high-dimensional lattices (e.g., Learning With Errors) | Most PQC finalists use this — good balance of security, key size, and performance |
| Hash-based | Security reduces to the properties of cryptographic hash functions (see [[Hashing]]) | Very conservative/well-understood security, but larger signature sizes (SPHINCS+) |
| Code-based | Hardness of decoding random linear error-correcting codes | Long-studied (McEliece cryptosystem dates to 1978), large public keys |
| Multivariate | Hardness of solving systems of multivariate polynomial equations | Several historical schemes broken, less trusted currently |
| Isogeny-based | Hardness of finding isogenies between elliptic curves | Very small keys, but a major scheme (SIKE) was broken classically in 2022, causing significant caution in this family |

## 7. Hybrid Approach — The Practical Migration Path

Rather than switching directly from classical to post-quantum algorithms, most current deployments use a **hybrid approach**: combining a classical algorithm (e.g., ECDH) with a post-quantum algorithm (e.g., Kyber/ML-KEM) simultaneously — an attacker would need to break **both** to compromise the connection. This provides safety against both current classical threats and future quantum threats, while PQC algorithms accumulate more real-world scrutiny.

Google Chrome and Cloudflare have already deployed hybrid **X25519+Kyber** key exchange in production TLS connections as an early real-world example of this transition.

## 8. Migration Considerations

- **Larger key/signature sizes** — PQC algorithms generally require significantly more bandwidth/storage than their classical counterparts (particularly SPHINCS+ signatures), impacting protocol design and performance.
- **Crypto-agility** — systems should be designed to swap algorithms without major re-architecture, since PQC standards may still evolve as cryptanalysis matures (this happened with SIKE's break in 2022 after it had reached the final NIST round).
- **Data with long confidentiality lifetimes** should prioritize migration first, given the "harvest now, decrypt later" threat.

## 9. Interview Questions

1. What quantum algorithm breaks RSA and ECC, and how? → **Shor's Algorithm — efficiently solves the factoring and discrete logarithm problems these algorithms rely on**
2. Does Grover's Algorithm break AES? → **No, it only halves the effective security margin (e.g., AES-128 → ~64-bit) — AES-256 remains strong**
3. Why is "harvest now, decrypt later" a concern even before quantum computers exist? → **Adversaries can record encrypted traffic today and decrypt it retroactively once quantum computers mature, threatening long-lived sensitive data**
4. Name NIST's standardized post-quantum key exchange algorithm. → **CRYSTALS-Kyber (ML-KEM)**
5. What mathematical family do most leading PQC algorithms rely on? → **Lattice-based problems**
6. What is a hybrid key exchange approach, and why is it used during migration? → **Combining a classical algorithm (e.g., ECDH) with a PQC algorithm (e.g., Kyber) so an attacker must break both — providing safety during the transition period**

## 10. Key Points

- **Shor's Algorithm** fully breaks RSA, DH, and ECC on a sufficiently powerful quantum computer; **Grover's Algorithm** only weakens symmetric crypto/hashing, which remain safe at larger key sizes (AES-256, SHA-384+).
- **"Harvest now, decrypt later"** makes migration urgent today for long-lived sensitive data, even before quantum computers are practically dangerous.
- NIST has standardized **CRYSTALS-Kyber (ML-KEM)** for key exchange and **CRYSTALS-Dilithium/SPHINCS+/FALCON** for signatures.
- Most real-world deployments use a **hybrid classical + post-quantum** approach during the transition.
- PQC is an active, evolving field — the 2022 break of SIKE (an isogeny-based finalist) is a reminder that new standards still need scrutiny.

---
*Related: [[Asymmetric_Encryption]], [[Elliptic_Curve_Cryptography]], [[Key_Exchange]], [[Digital_Signatures]]*
