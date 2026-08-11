## 1. Introduction

Data is the asset most security programs ultimately exist to protect. **Data classification** and **data security** practices ensure protection is applied proportionately — sensitive data gets strong controls, while low-sensitivity data isn't burdened with unnecessary overhead. Treating all data identically either over-protects (wasting resources) or under-protects (creating risk).

## 2. Data States — Where Data Needs Protection

| State | Description | Primary Protection |
|---|---|---|
| Data at Rest | Stored data — databases, files, backups | Encryption (disk/file-level, see [[Symmetric_Encryption]] in the Cryptography folder), access control |
| Data in Transit | Data actively moving across a network | Encryption in transit (TLS/VPN, see [[TLS_SSL]] and [[VPN]]) |
| Data in Use | Data actively being processed in memory/by an application | Access control, application security, increasingly **confidential computing** (hardware-based memory encryption during processing) |

All three states require protection — a system that only encrypts data at rest but transmits it in plaintext (or vice versa) has an incomplete data security posture.

## 3. Data Classification Levels

Organizations define tiers reflecting sensitivity, typically requiring different handling requirements at each level. A common commercial model:

| Level | Description | Example |
|---|---|---|
| Public | No harm if disclosed | Marketing materials, public website content |
| Internal | Not for public release, but limited harm if leaked | Internal memos, org charts |
| Confidential | Would cause meaningful harm if disclosed | Business plans, customer contracts, employee data |
| Restricted/Highly Confidential | Would cause severe harm — legal, financial, safety consequences | Trade secrets, PII/PHI, authentication credentials, encryption keys |

**Government/military model** uses a different, related tier system: **Unclassified → Confidential → Secret → Top Secret**, tied to formal clearance levels (connects to the MAC access control model covered in [[Identity_and_Access_Management]]).

## 4. Types of Sensitive Data

| Type | Definition | Regulatory Relevance |
|---|---|---|
| PII (Personally Identifiable Information) | Data that can identify a specific individual (name, SSN, email, address) | GDPR, various state/national privacy laws |
| PHI (Protected Health Information) | Health-related data tied to an individual | HIPAA (see [[Compliance_and_Frameworks]]) |
| PCI/Cardholder Data | Payment card numbers and related data | PCI-DSS |
| Intellectual Property (IP) | Trade secrets, proprietary designs, source code | Varies — often protected via contract/trade secret law rather than a specific regulation |
| Financial Data | Financial statements, transaction records | SOX and other financial regulations |

## 5. Data Ownership Roles (Recap)

See [[Security_Policies_and_Governance]] for full detail — the **data owner** decides classification and access requirements; the **data custodian** technically implements the resulting protections.

## 6. Data Loss Prevention (DLP)

Technology and processes designed to detect and prevent unauthorized transmission or exposure of sensitive data.

| DLP Type | Monitors |
|---|---|
| Network DLP | Data leaving the network (email, web uploads, file transfers) |
| Endpoint DLP | Data being copied to USB drives, printed, or moved on individual devices |
| Storage/Discovery DLP | Scanning data repositories to find sensitive data that may be improperly stored/exposed |
| Cloud DLP (CASB-integrated) | Data being uploaded to or shared via cloud applications |

**How DLP identifies sensitive data:** pattern matching (regex for credit card/SSN formats — see [[Regular_Expressions]] in the Linux folder), keyword/dictionary matching, and increasingly machine-learning-based content classification.

## 7. Encryption as a Data Security Control (Cross-Reference)

Data security relies heavily on cryptography covered in depth in the **Cryptography folder** — [[Symmetric_Encryption]] and [[Asymmetric_Encryption]] for confidentiality, [[Hashing]] and [[Digital_Signatures]] for integrity, [[Key_Management]] for the lifecycle securing the encryption itself.

## 8. Data Minimization and Retention

| Principle | Description |
|---|---|
| Data Minimization | Collect and retain only the data actually necessary for a defined purpose — less stored sensitive data means less exposure if a breach occurs |
| Data Retention Policy | Defines how long different data types must/may be kept before secure disposal — driven by both legal requirements (minimum retention) and risk reduction (maximum retention) |
| Right to Erasure | Under regulations like GDPR, individuals can request deletion of their personal data, subject to certain exceptions |

## 9. Secure Data Disposal

Simply deleting a file typically doesn't remove the underlying data (see [[File_Types_and_Inodes]] in the Linux folder) — proper disposal requires:

| Method | Use Case |
|---|---|
| Cryptographic Erasure | Deleting the encryption key that protects data, rendering the (still physically present) ciphertext permanently unrecoverable — fast, effective for already-encrypted data |
| Data Wiping/Overwriting | Overwriting storage media multiple times with patterns to prevent recovery |
| Degaussing | Using a strong magnetic field to erase magnetic storage media (not effective on SSDs) |
| Physical Destruction | Shredding, crushing, or incinerating storage media — the most certain method for end-of-life media |

## 10. Data Sovereignty and Residency

**Data sovereignty** refers to the principle that data is subject to the laws of the country/jurisdiction where it's collected or stored. **Data residency** refers to specific requirements/preferences about the physical/geographic location data must be stored in — increasingly relevant for multinational organizations navigating differing regional privacy laws (GDPR's restrictions on transferring EU data outside the EU being a prominent example).

## 11. Interview Questions

1. What are the three states of data, and why must all three be protected? → **At rest, in transit, and in use — protecting only some states leaves gaps an attacker can exploit (e.g., strong disk encryption but plaintext network transmission)**
2. What's the difference between PII and PHI? → **PII identifies an individual generally; PHI is specifically health-related information tied to an individual, governed by HIPAA**
3. What are the four types of DLP, based on what they monitor? → **Network, endpoint, storage/discovery, and cloud DLP**
4. Why is cryptographic erasure an effective and fast disposal method? → **It deletes the encryption key rather than the (much larger) ciphertext itself, instantly rendering the data unrecoverable without needing to overwrite the underlying storage**
5. What's the difference between data sovereignty and data residency? → **Sovereignty is the principle that data is subject to the laws of the jurisdiction where it resides; residency is the specific requirement/preference for where data must physically be stored**
6. Why does data classification matter for resource allocation? → **It ensures protection is proportionate — sensitive data gets strong (often costly) controls, while low-sensitivity data isn't over-protected unnecessarily**

## 12. Key Points

- Data must be protected across all three states: **at rest, in transit, and in use** — different mechanisms apply to each.
- **Classification levels** (Public → Internal → Confidential → Restricted) ensure proportionate protection, tied to defined **data owner/custodian** roles.
- **DLP** (network, endpoint, storage, cloud) technically enforces data handling policy by detecting/blocking unauthorized data movement.
- **Data minimization and retention policies** reduce exposure by limiting how much sensitive data exists and for how long.
- Secure disposal (**cryptographic erasure, wiping, physical destruction**) matters — deletion alone rarely removes data completely.

---
*Related: [[Security_Policies_and_Governance]], [[Compliance_and_Frameworks]], [[Symmetric_Encryption]] (Cryptography folder), [[Key_Management]] (Cryptography folder)*
