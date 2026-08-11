## 1. Introduction

**PKI (Public Key Infrastructure)** is the system of policies, roles, hardware, software, and procedures needed to create, manage, distribute, use, store, and revoke **digital certificates** and manage **public-key encryption**. PKI solves a critical problem asymmetric cryptography can't solve alone: **how do you know a public key really belongs to who it claims to belong to?**

## 2. The Trust Problem PKI Solves

Asymmetric crypto (see [[Asymmetric_Encryption]]) lets anyone publish a public key — but publishing a key doesn't prove identity. An attacker could publish a public key claiming to be "yourbank.com." PKI solves this by having a **trusted third party** vouch for the binding between an identity and a public key, via a signed **digital certificate**.

## 3. Core PKI Components

| Component | Role |
|---|---|
| CA (Certificate Authority) | Trusted entity that issues and digitally signs certificates, vouching for identity |
| RA (Registration Authority) | Verifies the identity of certificate requesters before the CA issues a cert (may be the CA itself or delegated) |
| Certificate | A signed document binding a public key to an identity |
| CRL (Certificate Revocation List) | A published list of certificates that have been revoked before their expiration |
| OCSP (Online Certificate Status Protocol) | A real-time protocol to check whether a specific certificate has been revoked |
| Certificate Repository | Where certificates and CRLs are published/stored for lookup |

## 4. X.509 Certificates

**X.509** is the standard format for digital certificates. A typical certificate contains:

| Field | Description |
|---|---|
| Subject | The entity the certificate identifies (e.g., domain name) |
| Issuer | The CA that issued and signed the certificate |
| Public Key | The subject's public key |
| Validity Period | Not Before / Not After dates |
| Serial Number | Unique identifier assigned by the CA |
| Signature Algorithm | The algorithm used by the CA to sign (e.g., SHA256withRSA) |
| Signature | The CA's digital signature over the certificate contents |
| Extensions | Additional data — Subject Alternative Names (SANs), Key Usage, Basic Constraints, etc. |

## 5. Chain of Trust

Certificates form a **hierarchical chain** rather than requiring everyone to trust every individual certificate directly:

```
Root CA (self-signed, trusted by OS/browser)
   └── Intermediate CA (signed by Root CA)
         └── End-Entity Certificate (e.g., yourbank.com — signed by Intermediate CA)
```

**Why intermediates exist:** Root CA private keys are kept extremely secure (often offline/air-gapped) since compromising one would be catastrophic. Intermediate CAs do the day-to-day signing, and if an intermediate is ever compromised, it can be revoked without invalidating the root.

**Verification process:** a browser verifies a server's certificate by checking the signature chain all the way up to a **Root CA it already trusts** (pre-installed in the OS/browser trust store). If the chain validates and no certificate is expired/revoked, the connection is trusted.

## 6. Types of Certificates (by Validation Level)

| Type | Validation Performed | Trust Indicator |
|---|---|---|
| DV (Domain Validation) | Only proves control over the domain | Lowest — fast/automated (e.g., Let's Encrypt) |
| OV (Organization Validation) | Verifies the requesting organization's legal existence | Moderate |
| EV (Extended Validation) | Rigorous organizational/legal identity verification | Highest — historically shown with special browser UI (largely deprecated in modern browsers) |

## 7. Certificate Revocation

Certificates can be compromised or become invalid before their expiration (e.g., private key leaked, organization details changed). Two mechanisms exist to check revocation status:

| Method | How it Works | Drawback |
|---|---|---|
| CRL | Client downloads a full list of revoked certificate serial numbers from the CA | Can be large, updated periodically (not real-time) |
| OCSP | Client queries the CA in real-time: "Is certificate X still valid?" | Adds a network round-trip; raises privacy concerns (CA learns which sites you visit) |
| OCSP Stapling | The server itself periodically fetches and "staples" a signed OCSP response to its certificate during the TLS handshake | Solves both the latency and privacy issues of plain OCSP — now the preferred approach |

## 8. Self-Signed Certificates

A certificate signed by its own private key rather than a trusted CA. Useful for internal testing/development, but **not trusted by default** by browsers/OSes — triggers security warnings, since there's no third party vouching for the identity. Common in internal enterprise networks (paired with a private internal CA trusted only within the organization) or for encrypting traffic where identity verification isn't the primary concern (e.g., some internal service-to-service traffic).

## 9. Web of Trust (Alternative Model)

Unlike PKI's hierarchical CA model, **PGP/GPG** uses a **Web of Trust** — a decentralized model where users sign each other's public keys directly, and trust is established through chains of personal vouching rather than a central authority. Common in the open-source/privacy community, though it hasn't achieved the mainstream adoption that CA-based PKI has for the web.

## 10. Real-World Use Cases

- **HTTPS/TLS** — the most visible PKI use case; browsers verify server certificates against trusted root CAs (see [[TLS_SSL]]).
- **Code signing certificates** — verify software publishers (Windows Authenticode, Apple Developer certs).
- **S/MIME** — email encryption/signing certificates.
- **Client certificates / mutual TLS (mTLS)** — used in zero-trust architectures where both client and server authenticate via certificates.
- **Enterprise internal CAs** — organizations run private CAs (e.g., via Active Directory Certificate Services) to issue certificates for internal systems, VPNs, and device/user authentication (802.1X).

## 11. Interview Questions

1. What problem does PKI solve that asymmetric encryption alone cannot? → **Verifying that a public key truly belongs to the identity it claims to**
2. What is a Certificate Authority's role? → **Verifying identity and digitally signing certificates to vouch for the binding between identity and public key**
3. Why do CAs use intermediate certificates instead of signing everything directly with the root? → **To keep the root CA's private key highly secure/offline; a compromised intermediate can be revoked without invalidating the root**
4. What's the difference between CRL and OCSP? → **CRL is a downloaded list of revoked certs; OCSP is a real-time query to check one certificate's status**
5. What does OCSP stapling solve? → **The latency and privacy issues of a client directly querying the CA via OCSP**
6. What is a self-signed certificate, and why does it trigger browser warnings? → **A certificate signed by its own key rather than a trusted CA — no third party vouches for its identity**

## 12. Key Points

- PKI binds public keys to verified identities via **digital certificates**, solving the "who does this key really belong to" problem.
- **X.509** is the standard certificate format; certificates are digitally signed by a **CA**.
- Trust flows through a **chain of trust** — Root CA → Intermediate CA → End-entity certificate.
- Revocation is checked via **CRL** or **OCSP** (with **OCSP stapling** as the modern best practice).
- PKI's hierarchical, CA-based trust model contrasts with PGP's decentralized **Web of Trust**.

---
*Related: [[Asymmetric_Encryption]], [[Digital_Signatures]], [[TLS_SSL]]*
