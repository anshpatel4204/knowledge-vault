## 1. Introduction

A **cryptographic hash function** takes an input (of any size) and produces a **fixed-size output** (the "hash" or "digest") that acts as a unique fingerprint of the data. Hashing is **one-way** — you cannot practically reverse a hash back into its original input. It underpins password storage, data integrity verification, digital signatures, and blockchain technology.

## 2. Properties of a Cryptographic Hash Function

A secure hash function must satisfy:

| Property | Description |
|---|---|
| Deterministic | Same input always produces the same output |
| Fast to compute | Efficient to generate a hash for any input |
| Pre-image resistance | Given a hash, it's infeasible to find the original input (one-way) |
| Second pre-image resistance | Given an input, it's infeasible to find a *different* input with the same hash |
| Collision resistance | It's infeasible to find *any* two different inputs that produce the same hash |
| Avalanche effect | A tiny change in input (even 1 bit) produces a drastically different, unpredictable output hash |

## 3. Common Hash Algorithms

| Algorithm | Output Size | Status |
|---|---|---|
| MD5 | 128-bit | **Broken** — collisions practical since 2004, must not be used for security |
| SHA-1 | 160-bit | **Broken** — first practical collision demonstrated (SHAttered, 2017), deprecated |
| SHA-2 (SHA-256, SHA-384, SHA-512) | 256/384/512-bit | Current standard, widely used and considered secure |
| SHA-3 (Keccak) | Variable (224-512-bit) | Newer standard, different internal structure (sponge construction) than SHA-2, used where algorithm diversity is desired |
| BLAKE2 / BLAKE3 | Variable | Modern, very fast, secure alternative gaining adoption |

## 4. Hash Digest Example

```
SHA-256("hello") = 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
SHA-256("Hello") = 185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969
```

Note the **avalanche effect**: capitalizing one letter completely changes the output — there is no visible relationship between similar inputs and their hashes.

## 5. Hashing vs Encryption

| Aspect | Hashing | Encryption |
|---|---|---|
| Reversible | No (one-way) | Yes, with the correct key |
| Key required | No (for plain hashing) | Yes |
| Output size | Fixed, regardless of input size | Proportional to input size |
| Purpose | Integrity, fingerprinting, password storage | Confidentiality |

## 6. Use Cases

### Password Storage

Passwords should **never** be stored in plaintext or with fast general-purpose hashes like plain SHA-256 (too easily brute-forced/rainbow-tabled at scale). Use **slow, purpose-built password hashing algorithms**:

| Algorithm | Notes |
|---|---|
| bcrypt | Widely used, built-in salt, adjustable work factor |
| scrypt | Memory-hard, resists GPU/ASIC cracking |
| Argon2 | Winner of the Password Hashing Competition (2015), current best-practice recommendation, memory-hard and tunable |
| PBKDF2 | Older, still acceptable, less resistant to GPU attacks than the above |

### File Integrity Verification

Publishing a SHA-256 checksum alongside a downloadable file lets users verify it wasn't corrupted or tampered with in transit.

### Digital Signatures

Instead of signing an entire large message (slow with asymmetric crypto), systems hash the message first and sign only the much smaller hash — see [[Digital_Signatures]].

### Blockchain

Each block contains the hash of the previous block, creating a tamper-evident chain — altering any historical block changes its hash and breaks every subsequent link.

### Data Deduplication & Fingerprinting

Identifying duplicate files/data quickly by comparing hashes instead of full contents.

## 7. Salting

A **salt** is random data added to a password *before* hashing, ensuring identical passwords produce different hashes and defeating precomputed **rainbow table** attacks.

```
hash = H(password + salt)
```

The salt is stored alongside the hash (it doesn't need to be secret, just unique per user) and is essential — without it, two users with the same password would have identical hashes, immediately revealing shared passwords.

## 8. Peppering

A **pepper** is an additional secret value (unlike a salt, kept secret and often shared across all users, e.g., stored in application config/HSM rather than the database) added to further protect against database-only breaches — an attacker who steals the password hash database still can't crack hashes without also obtaining the pepper.

## 9. Hash-Based Attacks

| Attack | Description |
|---|---|
| Brute Force | Trying every possible input until a matching hash is found |
| Dictionary Attack | Trying common passwords/wordlists against a hash |
| Rainbow Table | Precomputed hash-to-plaintext lookup tables; defeated by salting |
| Collision Attack | Finding two different inputs producing the same hash (breaks integrity guarantees, as demonstrated against MD5 and SHA-1) |
| Length Extension Attack | Exploits the internal structure of some hash functions (MD5, SHA-1, SHA-256) to append data and compute a valid new hash without knowing the original input — mitigated by using HMAC instead of naive hash concatenation, or by SHA-3's different construction |

## 10. Checksums vs Cryptographic Hashes

| Type | Purpose | Example |
|---|---|---|
| Checksum (CRC32) | Detect accidental corruption (not security) | Network/file transfer error checking |
| Cryptographic Hash | Detect intentional tampering, security-relevant | SHA-256, used in integrity/security contexts |

CRC32 is fast but trivially forgeable by an attacker — never use it where security matters.

## 11. Interview Questions

1. Is hashing reversible? → **No, it's a one-way function**
2. Name a broken hash algorithm still sometimes seen in legacy systems. → **MD5 or SHA-1**
3. What is the avalanche effect? → **A small input change causes a drastic, unpredictable change in the output hash**
4. Why shouldn't you use plain SHA-256 for password storage? → **It's too fast, making brute-force/rainbow table attacks practical at scale — use bcrypt/scrypt/Argon2 instead**
5. What does a salt protect against? → **Rainbow table attacks and identical hashes for identical passwords**
6. What's the difference between a checksum like CRC32 and a cryptographic hash? → **CRC32 only detects accidental corruption; cryptographic hashes resist intentional tampering**

## 12. Key Points

- Hash functions are **one-way**, deterministic, and produce a fixed-size digest.
- Key properties: pre-image resistance, second pre-image resistance, collision resistance, avalanche effect.
- **MD5 and SHA-1 are broken** — use **SHA-256/SHA-3** for integrity, and **bcrypt/scrypt/Argon2** specifically for passwords.
- **Salting** defeats rainbow tables; **peppering** adds a secret layer beyond the database.
- Hashing ≠ encryption — no key, no reversal, different purpose (integrity vs confidentiality).

---
*Related: [[Cryptography_Basics]], [[HMAC_and_MAC]], [[Digital_Signatures]], [[Encoding_vs_Encryption_vs_Hashing]]*
