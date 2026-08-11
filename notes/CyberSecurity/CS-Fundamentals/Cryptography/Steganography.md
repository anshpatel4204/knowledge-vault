## 1. Introduction

**Steganography** is the practice of **hiding the existence of a message** within an ordinary-looking carrier (an image, audio file, video, or even text), as opposed to cryptography, which scrambles a message's content but doesn't hide the fact that a secret message exists at all. The word derives from Greek: "steganos" (covered) + "graphein" (writing).

## 2. Steganography vs Cryptography — Key Distinction

| Aspect | Cryptography | Steganography |
|---|---|---|
| Goal | Make a message unreadable without a key | Hide the existence of a message entirely |
| Visibility | Ciphertext is visibly present (just unreadable) | Hidden data is not obviously detectable |
| If discovered | Still protected by the key/algorithm | Once discovered, the hidden message may be fully exposed (unless also encrypted) |
| Best practice | Used alone for confidentiality | Often **combined with encryption** — encrypt first, then hide the ciphertext, for defense in depth |

An encrypted file is obviously a "locked box" — clearly something is being protected, inviting scrutiny. A steganographic file looks like an ordinary vacation photo — nobody suspects there's anything to find in the first place.

## 3. Common Steganography Techniques

| Technique | Carrier | Description |
|---|---|---|
| LSB (Least Significant Bit) | Images | Replaces the least significant bit of pixel color values with hidden message bits — changes are imperceptible to the human eye |
| Audio Steganography | Audio files | Hides data in inaudible frequency ranges or slight amplitude variations |
| Video Steganography | Video files | Combines image and audio techniques across frames |
| Text Steganography | Text documents | Uses whitespace patterns, invisible Unicode characters, or subtle word choice/formatting variations |
| Metadata Steganography | File metadata (EXIF, etc.) | Hides data within a file's metadata fields rather than its visible content |
| Network Steganography | Protocol fields | Hides data in unused/optional protocol header fields, or timing patterns of packets |

## 4. LSB Steganography Example (Concept)

An 8-bit pixel color value like `10010110` can have its least significant bit changed to `10010111` — a difference of 1 in color value, invisible to the human eye, but that single bit can now carry one bit of a hidden message. Across thousands of pixels, an entire hidden message (or file) can be embedded without visibly altering the image.

## 5. Steganalysis — Detecting Hidden Data

**Steganalysis** is the counterpart discipline: detecting the *presence* of hidden data within a carrier file.

**Techniques:**

- Statistical analysis of pixel value distributions (LSB manipulation often creates detectable statistical anomalies).
- Comparing file size/characteristics against what's typical/expected for that file type.
- Signature-based detection for known steganography tool artifacts.
- Visual/audio inspection for anomalies (though modern techniques are designed to defeat this).

## 6. Real-World and Malicious Use Cases

**Legitimate uses:**

- Digital watermarking — embedding ownership/copyright information in media, resistant to casual removal.
- Covert communication in oppressive regimes where encrypted traffic alone draws suspicion.
- Fingerprinting distributed content to trace leaks.

**Malicious uses:**

- **Malware C2 (Command & Control)** — malware hiding configuration data or commands inside seemingly benign images downloaded from legitimate-looking sites, evading network content inspection that only flags obviously suspicious traffic.
- **Data exfiltration** — insiders or attackers hiding stolen data inside outbound image/document uploads to evade DLP (Data Loss Prevention) tools that scan for plaintext sensitive data patterns.
- **CTF (Capture The Flag) challenges** — steganography is a extremely common category in cybersecurity competitions, using tools to hide flags inside provided files.

## 7. Common Tools

| Tool | Purpose |
|---|---|
| Steghide | Embeds/extracts data in image and audio files, with optional passphrase-based encryption |
| OpenStego | GUI-based steganography with watermarking support |
| zsteg | Detects LSB steganography specifically in PNG/BMP files (popular in CTFs) |
| Binwalk | Analyzes files for embedded/hidden file signatures (firmware analysis, but also useful in stego contexts) |
| ExifTool | Reads/writes metadata, useful for metadata-based steganography and detection |

## 8. Combining Steganography with Cryptography

Best practice for genuinely sensitive covert communication: **encrypt the message first, then hide the resulting ciphertext using steganography.** This provides two independent layers — even if the hidden data is discovered (steganalysis defeats the hiding), it remains unreadable without the encryption key. Hiding alone (security through obscurity) is fragile once discovered; encryption alone is conspicuous. Together, they cover each other's weaknesses.

## 9. Limitations of Steganography

- **Not a substitute for encryption** — provides no mathematical guarantee of confidentiality if the hidden data is found.
- **Limited capacity** — carrier files can only hide a limited amount of data before statistical/visual anomalies become detectable.
- **Fragile to processing** — recompression, resizing, format conversion, or re-encoding of the carrier file can destroy the hidden data entirely.
- **Security through obscurity risk** — relying on "nobody will look" is a weak security model on its own.

## 10. Interview Questions

1. What's the core difference between steganography and cryptography? → **Cryptography hides the content of a message; steganography hides the existence of the message itself**
2. What is LSB steganography? → **Hiding data by modifying the least significant bit of pixel/audio sample values, imperceptibly**
3. What is steganalysis? → **The practice of detecting hidden data within a carrier file**
4. Why is combining steganography with encryption considered best practice? → **It provides defense in depth — even if the hidden data is found, it remains unreadable without the key**
5. Give a malicious real-world use of steganography. → **Malware hiding C2 commands or configuration inside images to evade network content inspection**
6. Why isn't steganography alone considered strong security? → **It relies on the data not being discovered — once found, there's no cryptographic protection unless combined with encryption**

## 11. Key Points

- Steganography hides the **existence** of a message; cryptography hides the **content** of a message.
- Common techniques: **LSB embedding** in images/audio, metadata hiding, network/text steganography.
- **Steganalysis** is the detection counterpart, often using statistical anomaly detection.
- Best practice: **encrypt then hide** — combining both provides defense in depth.
- Widely used in CTFs, malware C2 channels, and data exfiltration — a key area for security analysts to recognize.

---
*Related: [[Cryptography_Basics]], [[Symmetric_Encryption]], [[Encoding_vs_Encryption_vs_Hashing]]*
