## 1. Introduction

**Key exchange** protocols allow two parties to establish a **shared secret key** over a public, insecure channel — **without ever transmitting the secret itself** — even if an eavesdropper observes every message exchanged. This solves the core problem symmetric encryption can't solve on its own: how do two parties agree on a secret key in the first place?

## 2. Diffie-Hellman (DH) Key Exchange

The original and most famous key exchange protocol (1976), based on the difficulty of the **discrete logarithm problem**.

**Simplified process:**

1. Both parties publicly agree on a large prime `p` and a base/generator `g`.
2. Alice picks a private secret `a`, computes `A = g^a mod p`, sends `A` to Bob.
3. Bob picks a private secret `b`, computes `B = g^b mod p`, sends `B` to Alice.
4. Alice computes the shared secret: `s = B^a mod p`.
5. Bob computes the shared secret: `s = A^b mod p`.
6. Both arrive at the **same value `s`** — this becomes the shared symmetric key — without ever transmitting `a`, `b`, or `s` itself.

An eavesdropper sees `p`, `g`, `A`, and `B`, but cannot feasibly compute `s` without solving the discrete logarithm problem (finding `a` or `b` from `A` or `B`) — computationally infeasible for sufficiently large parameters.

## 3. ECDH (Elliptic Curve Diffie-Hellman)

The elliptic-curve variant of Diffie-Hellman — same underlying concept, but operating on elliptic curve points instead of modular exponentiation. Provides equivalent security to classic DH at much smaller key sizes, and is the modern standard used in TLS 1.3, Signal, and WireGuard.

## 4. Static vs Ephemeral Diffie-Hellman

| Variant | Description | Provides Forward Secrecy? |
|---|---|---|
| Static DH | Uses long-term, fixed key pairs for every session | No — if the long-term private key is ever compromised, all past sessions can be decrypted |
| Ephemeral DH (DHE / ECDHE) | Generates a **fresh key pair for every single session** | Yes — compromising a long-term key does not expose past session keys |

## 5. Forward Secrecy (Perfect Forward Secrecy — PFS)

**Forward secrecy** means that even if an attacker later steals a server's long-term private key, they **cannot decrypt previously captured traffic**, because each past session used its own unique, ephemeral, discarded key.

This is why **DHE** and **ECDHE** (the "E" for Ephemeral) are strongly preferred in modern TLS configurations over static RSA key exchange — RSA key exchange (where the client encrypts the session key directly with the server's static public key) has **no forward secrecy**: if the server's private key is ever compromised, an attacker who recorded past encrypted traffic can decrypt all of it retroactively.

## 6. Man-in-the-Middle Vulnerability of Plain DH

Plain Diffie-Hellman, by itself, provides no **authentication** — it only ensures confidentiality of the exchanged secret against passive eavesdroppers. An active attacker can perform a **MITM attack**: intercepting and replacing `A` and `B` with their own values, establishing separate shared secrets with each party while relaying (and reading/modifying) all traffic between them, undetected.

**Mitigation:** DH must be combined with **authentication** — typically via digital signatures and certificates (see [[Digital_Signatures]], [[PKI]]) — this is exactly what TLS does: the server signs its DH parameters using its certificate's private key so the client can verify they truly came from the legitimate server.

## 7. Key Exchange vs Encryption — Not the Same Thing

| Aspect | Key Exchange (DH/ECDH) | Asymmetric Encryption (RSA) |
|---|---|---|
| Purpose | Derive a shared secret collaboratively | Encrypt data directly using a public key |
| Who computes the final key | Both parties compute it together | Only the sender chooses/encrypts a value |
| Forward secrecy possible? | Yes, if ephemeral | No, if using static keys directly for encryption |

Diffie-Hellman doesn't encrypt anything itself — it only establishes a shared secret, which is then used as a symmetric key for actual encryption.

## 8. Key Exchange in TLS 1.3

TLS 1.3 **mandates** ephemeral key exchange (ECDHE) for every handshake, removing static RSA key exchange entirely — this was a deliberate design decision to guarantee forward secrecy for all TLS 1.3 connections by default (see [[TLS_SSL]] for the full handshake).

## 9. Post-Quantum Key Exchange

Classic DH/ECDH are vulnerable to future quantum computers (via Shor's Algorithm). NIST-standardized **CRYSTALS-Kyber** (now ML-KEM) is a lattice-based key encapsulation mechanism designed to resist quantum attacks, and is beginning to be deployed in hybrid configurations (classical + post-quantum) in modern browsers/protocols.

## 10. Interview Questions

1. What problem does Diffie-Hellman solve? → **Establishing a shared secret over an insecure channel without transmitting it directly**
2. What mathematical problem is classic DH's security based on? → **The discrete logarithm problem**
3. What does the "E" in DHE/ECDHE stand for, and why does it matter? → **Ephemeral — a fresh key pair per session, enabling forward secrecy**
4. What is forward secrecy? → **Compromise of a long-term private key doesn't expose past session traffic, because each session used a unique, discarded key**
5. Why is plain Diffie-Hellman vulnerable to MITM attacks? → **It provides no authentication on its own — an attacker can intercept and substitute their own DH values**
6. Does TLS 1.3 support static RSA key exchange? → **No — TLS 1.3 mandates ephemeral (EC)DHE for forward secrecy on every connection**

## 11. Key Points

- Key exchange lets two parties derive a **shared secret** over a public channel, without transmitting the secret itself.
- **Diffie-Hellman** (discrete log) and **ECDH** (elliptic curve) are the standard algorithms; ECDH is the modern default.
- **Ephemeral** variants (DHE/ECDHE) provide **forward secrecy** — a critical property static key exchange lacks.
- Plain DH has **no built-in authentication** — vulnerable to MITM unless combined with signatures/certificates.
- TLS 1.3 mandates ephemeral key exchange for every connection.

---
*Related: [[Asymmetric_Encryption]], [[TLS_SSL]], [[Digital_Signatures]], [[PKI]]*
