## How to Use This Folder

This roadmap gives the recommended reading order for the Cryptography folder — from historical/foundational concepts through symmetric and asymmetric crypto, integrity/authentication mechanisms, applied infrastructure (PKI/TLS), and finally attacks and forward-looking topics. Each note links to related ones via `[[wikilinks]]`, but this order avoids forward references to concepts you haven't met yet.

**Start here:** [[Cryptography_Basics]]
**Finish here:** [[Post_Quantum_Cryptography]]

## Stage 1 — Foundations

Vocabulary, historical grounding, and the distinctions that prevent later confusion.

1. [[Cryptography_Basics]] — goals, terminology, Kerckhoffs's principle
2. [[Classical_Ciphers]] — Caesar, Vigenère, One-Time Pad — where modern crypto's ideas came from
3. [[Encoding_vs_Encryption_vs_Hashing]] — clears up the most common real-world confusion before it can compound

## Stage 2 — Symmetric Cryptography

The faster, shared-key branch of cryptography, used for bulk data.

4. [[Symmetric_Encryption]] — AES, DES/3DES, block vs stream ciphers
5. [[Block_Cipher_Modes]] — ECB/CBC/CTR/GCM — how block ciphers handle real data

## Stage 3 — Asymmetric Cryptography

The public/private key branch, solving symmetric crypto's key distribution problem.

6. [[Asymmetric_Encryption]] — RSA, the public/private key concept
7. [[Elliptic_Curve_Cryptography]] — the modern, smaller-key alternative to RSA
8. [[Key_Exchange]] — Diffie-Hellman/ECDH, forward secrecy

## Stage 4 — Integrity, Authentication & Randomness

Confirming data hasn't changed and knowing who sent it — plus the randomness every prior stage silently depends on.

9. [[Hashing]] — one-way fingerprinting, password storage
10. [[HMAC_and_MAC]] — keyed integrity/authenticity
11. [[Digital_Signatures]] — non-repudiation via asymmetric crypto
12. [[Random_Number_Generation]] — why every key/nonce above must be unpredictable

## Stage 5 — Applied Infrastructure

Where the building blocks combine into real-world systems.

13. [[Key_Management]] — the lifecycle securing everything already covered
14. [[PKI]] — binding public keys to verified identities
15. [[TLS_SSL]] — the protocol tying nearly every prior topic together in practice

## Stage 6 — Attacks & Forward-Looking Topics

Where things break, how data can hide, and what's coming next.

16. [[Cryptographic_Attacks]] — brute force, padding oracle, side-channel, and more
17. [[Steganography]] — hiding the existence of data, distinct from encrypting it
18. [[Post_Quantum_Cryptography]] — why RSA/ECC won't last forever, and what replaces them

## Quick Reference — Full Order

Cryptography_Basics → Classical_Ciphers → Encoding_vs_Encryption_vs_Hashing → Symmetric_Encryption → Block_Cipher_Modes → Asymmetric_Encryption → Elliptic_Curve_Cryptography → Key_Exchange → Hashing → HMAC_and_MAC → Digital_Signatures → Random_Number_Generation → Key_Management → PKI → TLS_SSL → Cryptographic_Attacks → Steganography → Post_Quantum_Cryptography
