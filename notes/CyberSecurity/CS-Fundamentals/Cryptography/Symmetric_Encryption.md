## 1. Introduction

**Symmetric encryption** uses the **same key** for both encryption and decryption. It is fast and efficient, making it the workhorse for encrypting bulk data — but it comes with the **key distribution problem**: both parties must securely share the secret key before communicating, without an eavesdropper intercepting it.

## 2. How It Works

```
Plaintext + Secret Key → [Encryption Algorithm] → Ciphertext
Ciphertext + Same Secret Key → [Decryption Algorithm] → Plaintext
```

Both sender and receiver must possess the identical secret key. If the key is compromised, all data encrypted with it is compromised.

## 3. Block Ciphers vs Stream Ciphers

| Type | Description | Examples |
|---|---|---|
| Block Cipher | Encrypts data in fixed-size chunks (blocks), e.g. 128 bits at a time | AES, DES, 3DES, Blowfish |
| Stream Cipher | Encrypts data one bit/byte at a time, combining plaintext with a keystream | RC4, ChaCha20, Salsa20 |

Block ciphers require a **mode of operation** (see [[Block_Cipher_Modes]]) to handle data longer than one block securely.

## 4. AES (Advanced Encryption Standard)

The current global standard for symmetric encryption, selected by NIST in 2001 (originally the Rijndael cipher) to replace DES.

| Property | Value |
|---|---|
| Block size | 128 bits (fixed) |
| Key sizes | 128, 192, or 256 bits |
| Structure | Substitution-Permutation Network (SPN) |
| Rounds | 10 (AES-128), 12 (AES-192), 14 (AES-256) |
| Status | Considered secure; no practical break exists |

**AES round operations:** SubBytes (substitution via S-box) → ShiftRows (permutation) → MixColumns (diffusion) → AddRoundKey (XOR with round key) — repeated for the specified number of rounds.

**AES-NI:** Modern CPUs include hardware instructions (AES-NI) that dramatically accelerate AES operations, making it fast enough for full-disk and full-network encryption with negligible overhead.

## 5. DES and 3DES (Legacy)

- **DES (Data Encryption Standard)** — 64-bit block, only a **56-bit effective key** (8 bits used for parity). Broken by brute force as early as 1998 (EFF's "Deep Crack"). **Considered completely insecure today.**
- **3DES (Triple DES)** — applies DES three times (Encrypt-Decrypt-Encrypt, EDE) with either 2 or 3 keys to extend the effective key strength. Slower than AES and now **deprecated** (NIST disallowed for new use after 2023) due to a 64-bit block size vulnerable to birthday-bound attacks (e.g., Sweet32).

## 6. Other Notable Symmetric Ciphers

| Cipher | Notes |
|---|---|
| Blowfish | 64-bit block, variable key length (32-448 bits), designed by Bruce Schneier, largely superseded by AES |
| Twofish | AES finalist, 128-bit block, still considered strong |
| ChaCha20 | Modern stream cipher, faster than AES in software (no hardware acceleration needed), used in TLS 1.3 and WireGuard, often paired with Poly1305 for authentication (ChaCha20-Poly1305) |
| RC4 | Legacy stream cipher, has known statistical biases — **considered broken/deprecated**, historically used in WEP and older TLS |

## 7. Key Sizes and Brute Force Resistance

| Key Size | Approx. Brute Force Difficulty |
|---|---|
| 56-bit (DES) | Broken in hours/days with modern/dedicated hardware |
| 128-bit (AES-128) | Computationally infeasible with current technology |
| 256-bit (AES-256) | Effectively unbreakable by brute force, even accounting for future quantum reduction (~128-bit equivalent security against Grover's algorithm) |

## 8. Symmetric Encryption Requires an IV/Nonce

Most modes of operation require an **Initialization Vector (IV)** or **nonce** — a unique, often random value combined with the key to ensure identical plaintexts don't produce identical ciphertexts. **Reusing an IV/nonce with the same key is a critical vulnerability** in many modes (notably CTR and stream ciphers), potentially exposing plaintext entirely.

## 9. Advantages and Disadvantages

**Advantages:**
- Very fast, low computational overhead — ideal for large volumes of data.
- Simple mathematically compared to asymmetric crypto.
- Strong security at manageable key sizes (128/256-bit).

**Disadvantages:**
- **Key distribution problem** — securely sharing the key is the hard part.
- Doesn't scale well for many parties (n users need n(n-1)/2 unique key pairs for full pairwise secrecy).
- Provides confidentiality only — not inherently authentication or non-repudiation (needs to be paired with a MAC, see [[HMAC_and_MAC]]).

## 10. Symmetric vs Asymmetric — Quick Comparison

| Aspect | Symmetric | Asymmetric |
|---|---|---|
| Keys | 1 shared key | Public + private key pair |
| Speed | Fast | Slow |
| Use case | Bulk data encryption | Key exchange, signatures |
| Key distribution | Hard problem | Solved (public key can be shared openly) |

## 11. Real-World Use Cases

- **Disk/File encryption** — BitLocker, FileVault, VeraCrypt (AES).
- **VPN tunnels** — IPSec, WireGuard (AES, ChaCha20).
- **TLS bulk data** — after the handshake establishes a session key via asymmetric crypto, the actual data is encrypted symmetrically (AES-GCM, ChaCha20-Poly1305).
- **Wi-Fi security** — WPA2/WPA3 use AES (CCMP).

## 12. Interview Questions

1. What is the key difference between symmetric and asymmetric encryption? → **Symmetric uses one shared key for both operations; asymmetric uses a public/private key pair**
2. What is the current standard symmetric cipher? → **AES**
3. Why is DES considered insecure today? → **Its 56-bit key is small enough to brute force with modern hardware**
4. What is the "key distribution problem"? → **Securely sharing a symmetric secret key between parties before communication**
5. What's the difference between a block cipher and a stream cipher? → **Block cipher encrypts fixed-size chunks; stream cipher encrypts bit/byte by bit/byte**
6. Why is reusing an IV/nonce dangerous? → **It can expose patterns or fully reveal plaintext, especially in stream-based modes**

## 13. Key Points

- Symmetric encryption = one key for both encryption and decryption, fast, ideal for bulk data.
- **AES** is the modern standard (128/192/256-bit keys); **DES/3DES/RC4 are deprecated/broken**.
- Block ciphers need a mode of operation; stream ciphers encrypt continuously.
- The core weakness is the **key distribution problem** — solved in practice via hybrid encryption with asymmetric crypto.
- Never reuse an IV/nonce with the same key.

---
*Related: [[Cryptography_Basics]], [[Block_Cipher_Modes]], [[Asymmetric_Encryption]], [[TLS_SSL]]*
