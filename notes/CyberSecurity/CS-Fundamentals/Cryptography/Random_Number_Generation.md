## 1. Introduction

Nearly every cryptographic operation — key generation, IVs/nonces, salts, DH private values, ECDSA nonces — depends on **unpredictable randomness**. Weak randomness is one of the most common real-world causes of cryptographic failure (see [[Cryptographic_Attacks]] and [[Key_Management]]), because it undermines even mathematically perfect algorithms: if an attacker can predict or narrow down a "random" value, the security guarantees collapse regardless of key size.

## 2. Entropy — The Foundation of Randomness

**Entropy** is a measure of unpredictability/randomness. Cryptographic systems need sources of **high-entropy** data (unpredictable to any observer) to seed random number generation. Common entropy sources on real systems:

- Hardware noise (thermal noise, electrical jitter)
- Timing variations (keyboard/mouse input timing, disk I/O timing, interrupt timing)
- Dedicated hardware RNG chips (Intel RDRAND/RDSEED instructions)
- Environmental sensors (in some specialized/embedded contexts)

## 3. TRNG vs PRNG vs CSPRNG

| Type | Description | Cryptographically Safe? |
|---|---|---|
| TRNG (True Random Number Generator) | Derives randomness directly from a physical/hardware entropy source | Yes, if the entropy source is genuinely unpredictable |
| PRNG (Pseudo-Random Number Generator) | Deterministic algorithm producing output that *looks* random from a seed, but is fully predictable if the seed is known | **No** — unsafe for security purposes (e.g., `Math.random()` in most languages, the classic C `rand()`) |
| CSPRNG (Cryptographically Secure PRNG) | A PRNG specifically designed so that, even with knowledge of some outputs, predicting future/past outputs is computationally infeasible | **Yes** — the correct choice for all security-sensitive randomness |

**Key distinction:** a plain PRNG is built for statistical randomness (good for simulations, games) but is often fast and predictable once the seed/internal state is known. A **CSPRNG** is specifically engineered to resist an adversary trying to predict its output, even with partial knowledge.

## 4. Common CSPRNGs in Practice

| Source | Platform |
|---|---|
| `/dev/urandom` (and `/dev/random`) | Linux/Unix — kernel-managed CSPRNG, seeded from system entropy pool |
| `CryptGenRandom` / `BCryptGenRandom` | Windows |
| `SecRandomCopyBytes` | macOS/iOS |
| `crypto.getRandomValues()` | Browser JavaScript |
| `os.urandom()` / `secrets` module | Python (never use the plain `random` module for security purposes) |
| `RAND_bytes()` (OpenSSL) | General cryptographic libraries |

**Rule of thumb:** always use the platform/language's dedicated cryptographic random API — never a general-purpose `random()` function — for keys, tokens, salts, IVs, or nonces.

## 5. What Randomness Is Used For in Cryptography

| Use | Requirement |
|---|---|
| Symmetric/asymmetric key generation | Must be unpredictable — a guessable key defeats the entire algorithm |
| IVs (Initialization Vectors) | Must typically be unpredictable and/or unique per encryption (mode-dependent, see [[Block_Cipher_Modes]]) |
| Nonces | Must be unique (never repeated) for a given key, critical in CTR/GCM |
| Salts | Must be unique per password, doesn't need to be secret, just unpredictable/unique (see [[Hashing]]) |
| DH/ECDH private values | Must be unpredictable — a guessable private exponent leaks the shared secret |
| ECDSA per-signature nonce (k) | Must be unique and unpredictable per signature — reuse or bias leaks the private key |
| Session tokens / CSRF tokens | Must be unpredictable to prevent guessing/hijacking |

## 6. Real-World Randomness Failures

| Incident | Description |
|---|---|
| Debian OpenSSL Bug (2008) | A patch accidentally removed most entropy sources from Debian's OpenSSL PRNG seeding, reducing the effective keyspace of generated SSH/SSL keys to only a few thousand possibilities — millions of weak keys were generated and later cataloged/brute-forced by researchers |
| Sony PS3 ECDSA Key Leak (2010) | Sony reused the same "random" nonce `k` for every ECDSA signature instead of generating a fresh one each time, allowing hackers to solve for and fully recover Sony's private signing key |
| Dual_EC_DRBG Concerns (2013) | A NIST-standardized CSPRNG was later suspected (based on leaked NSA documents) to contain a potential backdoor allowing prediction of its output if you knew a certain secret constant — RSA Security had used it as a default in a product, prompting industry-wide distrust and removal |
| Android Bitcoin Wallet Vulnerability (2013) | A flaw in Android's `SecureRandom` implementation caused insufficient entropy in some cases, leading to predictable ECDSA nonces and stolen Bitcoin from affected wallets |

## 7. Predictable Seeding — A Common Anti-Pattern

Seeding a PRNG with predictable values (current timestamp, process ID, a hardcoded value) makes its entire output sequence guessable by an attacker who can estimate or brute-force the seed space — even if the PRNG algorithm itself is otherwise fine. This is a distinct failure mode from using the wrong *type* of RNG entirely.

```
BAD:  seed = current_time()          → guessable within a narrow window
BAD:  key = MD5(username + "salt")   → deterministic, not random at all
GOOD: key = CSPRNG(32 bytes)         → unpredictable, high entropy
```

## 8. Entropy Starvation

On some systems (especially freshly-booted virtual machines, containers, or embedded/IoT devices with few input sources), the available entropy pool can be **too small** immediately after boot, before enough unpredictable events (mouse movement, disk timing, network jitter) have accumulated. Generating keys too early in such conditions has historically produced weak, sometimes duplicate keys across different devices. Modern systems mitigate this using hardware RNG instructions (RDRAND) and persisting entropy across reboots.

## 9. Interview Questions

1. What's the difference between a PRNG and a CSPRNG? → **A CSPRNG is specifically designed to resist prediction of its output even with partial knowledge; a plain PRNG is only built for statistical randomness, not security**
2. Why shouldn't you use `Math.random()` or `rand()` for generating a session token? → **They're not cryptographically secure — outputs can often be predicted once the internal state/seed is known**
3. What real-world incident resulted from a Linux distribution accidentally weakening its RNG seeding? → **The 2008 Debian OpenSSL bug**
4. What happened when Sony reused an ECDSA nonce across signatures? → **Their private signing key was fully recoverable, allowing PS3 jailbreaking/piracy**
5. What is entropy starvation? → **Insufficient accumulated randomness (especially right after boot) leading to weak/predictable key generation**
6. Does a salt need to be secret? → **No — it needs to be unique/unpredictable, not secret; its purpose is to defeat rainbow tables and ensure identical passwords hash differently**

## 10. Key Points

- Strong cryptography depends entirely on **unpredictable randomness** for keys, IVs, nonces, salts, and per-signature values.
- Always use a **CSPRNG** (`/dev/urandom`, `crypto.getRandomValues()`, language `secrets` modules) — never a general-purpose PRNG — for anything security-sensitive.
- **Predictable seeding** and **nonce reuse** are common, catastrophic real-world failure patterns (Debian OpenSSL bug, Sony PS3 key leak).
- **Entropy starvation** at boot time can weaken key generation on some systems.
- Weak randomness is one of the most frequent root causes of real-world cryptographic breaches — more common than broken algorithms themselves.

---
*Related: [[Key_Management]], [[Cryptographic_Attacks]], [[Digital_Signatures]], [[Hashing]]*
