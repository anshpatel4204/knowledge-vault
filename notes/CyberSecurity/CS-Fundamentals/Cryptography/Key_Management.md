## 1. Introduction

**Key management** is the set of processes and infrastructure for handling cryptographic keys throughout their entire existence — generation, distribution, storage, use, rotation, and destruction. Nearly every real-world cryptographic failure traces back to a key management mistake rather than a broken algorithm (see [[Cryptographic_Attacks]]), making this arguably the most practically important topic in applied cryptography.

## 2. The Key Lifecycle

```
Generation → Distribution → Storage → Usage → Rotation → Revocation → Destruction/Archival
```

| Stage | Description |
|---|---|
| Generation | Creating a cryptographically strong key using a CSPRNG (see [[Random_Number_Generation]]) |
| Distribution | Securely delivering the key to authorized parties without exposure |
| Storage | Protecting the key at rest (HSM, encrypted vault, restricted access) |
| Usage | Applying the key only for its intended purpose, within policy limits |
| Rotation | Periodically replacing keys to limit the impact of undetected compromise |
| Revocation | Invalidating a key before its scheduled expiration (e.g., after suspected compromise) |
| Destruction/Archival | Securely deleting keys no longer needed, or archiving for compliance/decryption of historical data |

## 3. Key Generation

Keys must be generated with sufficient **entropy** using a **Cryptographically Secure Pseudo-Random Number Generator (CSPRNG)** — never with predictable seeds, timestamps, or weak PRNGs (see [[Random_Number_Generation]] for full detail). Key generation should also respect algorithm-specific requirements (e.g., RSA primes must pass strong primality tests; ECC keys must be valid points on the chosen curve).

## 4. Key Distribution

| Method | Description |
|---|---|
| Manual/Out-of-band | Physically delivering keys (couriers, secure printed material) — used for very high-security root keys |
| Key Exchange Protocols | Diffie-Hellman/ECDH derive a shared key over an insecure channel without transmitting it (see [[Key_Exchange]]) |
| Key Wrapping | Encrypting one key with another ("key encryption key," KEK) for secure transport/storage |
| PKI-based Distribution | Public keys distributed via certificates; private keys never leave their owner (see [[PKI]]) |
| Key Management Service (KMS) | Cloud/enterprise services (AWS KMS, Azure Key Vault, HashiCorp Vault) that generate, store, and control access to keys via API, with audit logging |

## 5. Key Storage

Where and how a key is stored is often the single biggest determinant of real-world security.

| Storage Method | Security Level | Notes |
|---|---|---|
| Hardcoded in source code | **Very poor** | Extremely common real-world vulnerability — keys end up in version control history, decompiled binaries, mobile apps |
| Plaintext config file | Poor | Vulnerable to any file system compromise |
| Environment variables | Moderate | Better than hardcoding, but can leak via process listings, crash dumps, logging |
| Encrypted at rest (application-level) | Good | Key itself still needs protecting — "turtles all the way down" problem, addressed by KEKs/HSMs |
| KMS / Secrets Manager | Strong | Centralized, access-controlled, audited, supports rotation |
| HSM (Hardware Security Module) | Strongest | Dedicated tamper-resistant hardware; private keys often **never leave the device** — operations (signing, decryption) happen inside the HSM itself |

## 6. HSMs (Hardware Security Modules)

Dedicated physical devices designed to generate, store, and use cryptographic keys with strong tamper resistance (including automatic key destruction if physical tampering is detected). Used for root CA keys, payment processing (PCI-DSS compliance), and any scenario where key extraction would be catastrophic.

**Cloud equivalents:** AWS CloudHSM, Azure Dedicated HSM, Google Cloud HSM — provide HSM-backed key management without owning physical hardware.

## 7. Key Rotation

Periodically replacing keys **limits the blast radius** of an undetected compromise — an attacker with an old, rotated-out key gains nothing going forward. Rotation frequency depends on key sensitivity and regulatory requirements (e.g., PCI-DSS often mandates annual rotation for certain keys).

**Challenges with rotation:**

- Data encrypted under an old key must either be re-encrypted under the new key or the old key must be retained (securely) to decrypt historical data.
- Coordinating rotation across distributed systems without downtime requires careful **key versioning**.

## 8. Key Escrow and Backup

**Key escrow** deliberately stores a copy of a key (or key-recovery material) with a trusted third party, enabling recovery if the primary key is lost — but also creates a new potential point of compromise and, in government contexts, has historically been controversial (e.g., the 1990s Clipper Chip proposal, which mandated law-enforcement key escrow and was ultimately abandoned due to security and civil liberties concerns).

**Split knowledge / dual control:** sensitive keys (especially HSM root keys) are often split among multiple custodians using **Shamir's Secret Sharing**, requiring a threshold number of custodians (e.g., 3-of-5) to reconstruct the key — preventing any single person from having full access.

## 9. Key Revocation

When a key is suspected or known to be compromised, it must be revoked before its natural expiration:

- For certificates: via **CRL** or **OCSP** (see [[PKI]]).
- For symmetric/API keys: immediate deactivation in the KMS/secrets manager, followed by re-issuance and re-distribution to legitimate parties.

## 10. Key Destruction

Keys no longer needed should be securely destroyed (cryptographic erasure — deleting the key that protects encrypted data, rather than needing to wipe the data itself, is often used for compliance-driven "right to erasure" scenarios). For HSM-resident keys, destruction is typically a built-in, auditable HSM operation.

## 11. Common Key Management Failures (Real-World)

| Failure | Consequence |
|---|---|
| Hardcoded API/encryption keys in public GitHub repos | Automated bots scan and exploit exposed keys within minutes of a push |
| Reusing the same key across environments (dev/staging/prod) | A breach in a lower-security environment compromises production |
| No key rotation policy | A single old compromise remains exploitable indefinitely |
| Weak or predictable key generation | Debian's 2008 OpenSSL PRNG bug drastically reduced the keyspace of generated SSH/TLS keys, making many practically guessable |
| No revocation plan | Compromised keys/certificates remain trusted far longer than necessary |

## 12. Interview Questions

1. What are the main stages of the key lifecycle? → **Generation, distribution, storage, usage, rotation, revocation, destruction**
2. Why is an HSM considered stronger than storing keys in an encrypted config file? → **Keys often never leave the HSM itself; cryptographic operations happen inside tamper-resistant hardware**
3. Why is key rotation important even if no compromise is known? → **It limits the impact/blast radius of an undetected compromise going forward**
4. What is key escrow, and what's a historical controversy tied to it? → **Storing a recovery copy of a key with a trusted third party; the 1990s Clipper Chip proposal for mandated law-enforcement escrow was controversial and abandoned**
5. What technique splits a key among multiple custodians requiring a threshold to reconstruct it? → **Shamir's Secret Sharing**
6. What's the most common real-world key management mistake? → **Hardcoding keys in source code, especially ones pushed to public repositories**

## 13. Key Points

- Key management covers the full lifecycle: **generate → distribute → store → use → rotate → revoke → destroy**.
- **HSMs and KMS/secrets managers** are the gold standard for key storage — never hardcode keys.
- **Rotation** limits the impact of undetected key compromise; **revocation** handles known compromise.
- **Key escrow** and **Shamir's Secret Sharing** address recovery and split-control scenarios, each with their own trade-offs.
- The overwhelming majority of real-world cryptographic failures are **key management failures**, not broken algorithms.

---
*Related: [[Random_Number_Generation]], [[PKI]], [[Cryptographic_Attacks]], [[Key_Exchange]]*
