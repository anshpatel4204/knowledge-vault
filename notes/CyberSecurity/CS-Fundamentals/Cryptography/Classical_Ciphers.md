## 1. Introduction

**Classical ciphers** are pre-computer-era encryption techniques, operating on letters rather than bits. They're no longer used for real security (all are trivially broken with modern computing/statistical tools), but they remain essential for understanding the foundational concepts — substitution, transposition, key-based transformation — that modern ciphers like AES still build on internally. They're also a staple of CTFs and cryptography fundamentals courses.

## 2. Substitution Ciphers

Replace each symbol in the plaintext with another symbol, according to a fixed system.

### Caesar Cipher
Shifts each letter by a fixed number of positions in the alphabet.

```
Plaintext:  HELLO
Shift:      3
Ciphertext: KHOOR
```

Only **25 possible keys** (shifts) for the English alphabet — trivially broken by brute force or frequency analysis.

### Monoalphabetic Substitution Cipher
Each letter maps to a unique different letter via a fixed substitution alphabet (not just a shift). Keyspace is `26!` (~4×10^26), sounds strong, but is **completely broken by frequency analysis** — since letter frequencies in natural language (E, T, A being most common in English) leak through the substitution unchanged.

### Vigenère Cipher
A **polyalphabetic** substitution cipher using a repeating keyword — each letter's shift amount depends on the corresponding letter of the key.

```
Plaintext:  ATTACKATDAWN
Key:        LEMONLEMONLE
Ciphertext: LXFOPVEFRNHR
```

Historically called *"le chiffre indéchiffrable"* (the indecipherable cipher) — resisted cryptanalysis for centuries until **Kasiski's examination** (finding repeated ciphertext sequences to deduce key length) and later **frequency analysis per key position** broke it in the 19th century.

### ROT13
A special case of the Caesar cipher with a fixed shift of 13 — self-inverse (applying it twice returns the original text). Used historically for obscuring spoilers/answers online, not for any real security purpose.

## 3. Transposition Ciphers

Rearrange the **order** of characters without changing the characters themselves.

### Rail Fence Cipher
Writes the message in a zigzag pattern across a set number of "rails," then reads off row by row.

```
Plaintext: WEAREDISCOVEREDFLEEATONCE  (3 rails)

W . . . E . . . C . . . R . . . L . . . T . . . E
. E . R . D . S . O . E . E . F . E . A . O . C .
. . A . . . I . . . V . . . D . . . E . . . N . .

Ciphertext: WECRLTEERDSOEEFEAOCAIVDEN
```

### Columnar Transposition
Writes the plaintext into a grid (rows) under a keyword, then reads the columns out in an order determined by alphabetizing the keyword's letters.

## 4. Other Notable Classical Ciphers

| Cipher | Type | Notes |
|---|---|---|
| Playfair Cipher | Substitution (digraph-based) | Encrypts pairs of letters using a 5x5 key grid, harder to break via simple frequency analysis than monoalphabetic substitution, used historically by the British military |
| ADFGVX Cipher | Substitution + Transposition | Used by the German army in WWI, combined a substitution grid with columnar transposition |
| Enigma Machine | Polyalphabetic substitution (electromechanical) | WWII German cipher machine, famously broken by Allied cryptanalysts (Alan Turing and the Bletchley Park team), a pivotal event in both WWII and the history of computing |
| Atbash Cipher | Substitution | Reverses the alphabet (A↔Z, B↔Y, ...), ancient Hebrew origin, purely educational today |

## 5. One-Time Pad (OTP) — The One Classical Cipher That IS Unbreakable

The **One-Time Pad** combines plaintext with a truly random key **of equal length**, used **only once**, via modular addition (or XOR for binary data).

```
Ciphertext = Plaintext XOR Key   (Key is truly random, same length as plaintext, never reused)
```

**Claude Shannon mathematically proved the OTP provides perfect secrecy** — given the ciphertext alone, every possible plaintext of that length is equally likely, so no amount of computing power can break it.

**Why it's not used practically:**
- The key must be **truly random** (not pseudorandom) and **as long as the message itself**.
- The key must be **shared securely in advance** and **never reused** — reusing an OTP key even once completely breaks its security (this is exactly what happened in the real-world VENONA project, where Soviet cipher clerks reused one-time pad material, allowing US/UK cryptanalysts to partially break supposedly unbreakable Soviet communications).
- Securely distributing a key as long as the message defeats much of the practical purpose of encryption in the first place.

## 6. Why Classical Ciphers Are Broken Today

| Weakness | Description |
|---|---|
| Small keyspace | Caesar cipher has only 25 possible keys — brute-forceable instantly |
| Frequency analysis | Letter/digraph/trigraph frequency patterns in natural language survive simple substitution, revealing the key statistically |
| Known-plaintext vulnerability | Even a small amount of known plaintext can reveal the entire substitution/transposition scheme |
| No diffusion/confusion at scale | Unlike AES's rounds of substitution+permutation, classical ciphers don't sufficiently obscure statistical relationships between plaintext and ciphertext |

## 7. Link to Modern Cryptography

Modern block ciphers like AES are built on the same two fundamental operations classical ciphers pioneered — **substitution** (AES's S-boxes, similar in spirit to monoalphabetic substitution) and **permutation/transposition** (AES's ShiftRows/MixColumns) — but applied across many rounds, on binary data, with enormous keyspaces, and specifically engineered (via **Shannon's confusion and diffusion principles**) to eliminate the statistical patterns that broke classical ciphers.

## 8. Interview Questions

1. What's the difference between a substitution cipher and a transposition cipher? → **Substitution replaces symbols; transposition rearranges their order**
2. Why is the Caesar cipher trivially breakable? → **Only 25 possible keys — brute force takes seconds**
3. What cryptanalysis technique broke the Vigenère cipher? → **Kasiski examination (finding key length) followed by frequency analysis per key position**
4. What is the only classical cipher proven to be mathematically unbreakable, and who proved it? → **The One-Time Pad, proven by Claude Shannon**
5. Why isn't the One-Time Pad used in practice despite being unbreakable? → **The key must be truly random, as long as the message, shared securely in advance, and never reused — impractical at scale**
6. What real-world cryptanalysis project exploited OTP key reuse? → **The VENONA project, against reused Soviet one-time pads**

## 9. Key Points

- Classical ciphers = **substitution** (Caesar, Vigenère, Playfair) and **transposition** (Rail Fence, Columnar) — both broken by frequency analysis or brute force today.
- **Vigenère** resisted cryptanalysis for centuries before Kasiski's method broke it.
- The **One-Time Pad** is the only cipher with mathematically proven perfect secrecy (Shannon) — but its key management requirements make it impractical for general use.
- Modern ciphers (AES) still use substitution and permutation internally, just at massive scale with strong mathematical guarantees against the statistical attacks that broke classical ciphers.

---
*Related: [[Cryptography_Basics]], [[Symmetric_Encryption]], [[Cryptographic_Attacks]]*
