## 1. Introduction

**TLS (Transport Layer Security)** is the cryptographic protocol that secures data in transit across networks — most visibly as the "S" in HTTPS. It provides **confidentiality**, **integrity**, and **authentication** by combining nearly every concept in this folder: symmetric encryption, asymmetric key exchange, digital certificates, and MACs/AEAD. **SSL (Secure Sockets Layer)** is its predecessor — SSL is now fully deprecated and insecure; "SSL" is still used colloquially but modern systems run TLS.

## 2. SSL/TLS Version History

| Version | Status |
|---|---|
| SSL 2.0 / 3.0 | **Deprecated, insecure** (POODLE attack broke SSL 3.0) |
| TLS 1.0 / 1.1 | **Deprecated** (vulnerable to BEAST, weak cipher support), disabled by major browsers |
| TLS 1.2 | Still widely used and secure when configured correctly (strong cipher suites, no legacy fallback) |
| TLS 1.3 | **Current standard** — faster handshake, removes legacy/weak algorithms entirely, mandates forward secrecy |

## 3. What TLS Provides

| Goal | Mechanism |
|---|---|
| Confidentiality | Symmetric encryption (AES-GCM, ChaCha20-Poly1305) for the actual data |
| Integrity | MAC/AEAD authentication tags |
| Authentication | Server (and optionally client) certificates verified via PKI |
| Forward Secrecy | Ephemeral key exchange (ECDHE) — mandatory in TLS 1.3 |

## 4. The TLS 1.2 Handshake (Simplified)

1. **ClientHello** — client sends supported TLS versions, cipher suites, and a random value.
2. **ServerHello** — server picks a cipher suite, sends its own random value, and its **certificate**.
3. **Key Exchange** — server (and optionally client) sends key exchange parameters (e.g., ECDHE public value); client verifies the server's certificate against a trusted CA (see [[PKI]]).
4. **Pre-Master Secret** — client generates and sends key exchange material, both sides derive the same **master secret** using the exchanged randoms and key exchange values.
5. **Finished** — both sides send a MAC over the entire handshake so far, encrypted with the newly derived session keys, confirming the handshake wasn't tampered with.
6. **Application Data** — the actual HTTP traffic is now encrypted using symmetric session keys derived from the master secret.

This takes **2 round-trips (2-RTT)** before application data can flow.

## 5. TLS 1.3 Handshake — Faster and Stricter

TLS 1.3 significantly simplifies and secures the handshake:

- **1-RTT handshake** by default — client sends its key share alongside the ClientHello, cutting one round trip compared to TLS 1.2.
- **0-RTT (optional)** — for resumed connections, data can be sent immediately using a previously established key (with some replay-attack caveats for non-idempotent requests).
- **Removes legacy/weak algorithms entirely** — no RC4, no static RSA key exchange, no CBC-mode ciphers, no SHA-1, no compression (which enabled CRIME attacks).
- **Mandatory forward secrecy** — every handshake uses ephemeral (EC)DHE (see [[Key_Exchange]]); static key exchange is not permitted.
- **Encrypts more of the handshake itself** (e.g., certificates are encrypted after the initial key exchange), improving privacy against passive eavesdroppers.

## 6. Cipher Suites

A **cipher suite** specifies the exact combination of algorithms used in a TLS session, historically formatted like:

```
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
     |         |        |         |
Key Exchange  Auth   Bulk Cipher  MAC/PRF
```

| Component | Role |
|---|---|
| Key Exchange (ECDHE) | How the session key is established |
| Authentication (RSA/ECDSA) | How the server proves its identity (via its certificate's signature algorithm) |
| Bulk Cipher (AES-256-GCM) | How the actual application data is encrypted |
| MAC/Hash (SHA384) | Used in the handshake's key derivation/PRF |

TLS 1.3 simplified cipher suite naming since it removed key exchange/auth algorithm choices from the suite itself (only AEAD ciphers like `TLS_AES_256_GCM_SHA384` remain).

## 7. Certificates in TLS

The server presents an **X.509 certificate** (see [[PKI]]) during the handshake, containing its public key and identity, signed by a CA. The client validates:

1. The certificate chain leads to a **trusted root CA**.
2. The certificate hasn't **expired**.
3. The certificate hasn't been **revoked** (via CRL/OCSP, often OCSP stapling).
4. The certificate's **Subject/SAN** matches the domain being accessed.

**Mutual TLS (mTLS)** extends this so the client also presents a certificate, allowing the server to authenticate the client too — common in zero-trust architectures and service-to-service (API) authentication.

## 8. Session Resumption

To avoid a full handshake on every connection, TLS supports resuming a previous session:

| Method | Description |
|---|---|
| Session IDs | Server caches session state, client references it by ID on reconnect |
| Session Tickets | Server encrypts session state into a ticket handed to the client, avoiding server-side storage |
| PSK / 0-RTT (TLS 1.3) | Uses a Pre-Shared Key derived from a prior session to skip most of the handshake |

## 9. Common TLS Attacks (Historical Context)

| Attack | Target |
|---|---|
| POODLE | SSL 3.0's CBC padding — forced downgrade attacks |
| BEAST | TLS 1.0's CBC mode implementation weakness |
| Heartbleed | OpenSSL implementation bug (buffer over-read), not a protocol flaw itself, but devastating — leaked server memory including private keys |
| CRIME / BREACH | Exploited TLS/HTTP compression to infer secrets via response size |
| DROWN | Cross-protocol attack leveraging SSLv2 support to break RSA keys shared with TLS |
| Downgrade attacks | Forcing a connection to negotiate an older, weaker TLS/SSL version |

These are largely why **TLS 1.3 aggressively removed** compression, CBC ciphers, and legacy negotiation flexibility.

## 10. Practical Commands

```bash
# Inspect a site's certificate and negotiated TLS version
openssl s_client -connect example.com:443 -tls1_3

# View certificate details
openssl x509 -in cert.pem -text -noout

# Check for supported cipher suites
nmap --script ssl-enum-ciphers -p 443 example.com
```

## 11. Interview Questions

1. What's the difference between SSL and TLS? → **SSL is the deprecated predecessor; TLS is the current, secure protocol — "SSL" is often used colloquially to mean TLS**
2. Which TLS version mandates forward secrecy on every connection? → **TLS 1.3**
3. How many round trips does a full TLS 1.3 handshake typically take vs TLS 1.2? → **TLS 1.3: 1-RTT (or 0-RTT for resumption); TLS 1.2: 2-RTT**
4. What does a cipher suite specify? → **The combination of key exchange, authentication, bulk encryption, and MAC algorithms used**
5. What is mTLS? → **Mutual TLS — both client and server present certificates to authenticate each other**
6. What real-world bug caused massive private key leakage via a TLS library flaw? → **Heartbleed (OpenSSL buffer over-read vulnerability)**

## 12. Key Points

- TLS provides confidentiality, integrity, and authentication by combining symmetric encryption, key exchange, and PKI.
- **TLS 1.3** is the current standard — faster (1-RTT), removes legacy weak algorithms, mandates forward secrecy.
- The handshake establishes a shared session key via (EC)DHE, authenticated by the server's certificate.
- **Cipher suites** define the exact algorithm combination used per connection.
- Historical attacks (POODLE, BEAST, Heartbleed, CRIME) drove the aggressive simplification seen in TLS 1.3.

---
*Related: [[PKI]], [[Key_Exchange]], [[Symmetric_Encryption]], [[Block_Cipher_Modes]], [[Digital_Signatures]]*
