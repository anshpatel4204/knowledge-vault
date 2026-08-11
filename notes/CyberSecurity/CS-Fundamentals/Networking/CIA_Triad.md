## 1. Introduction

The **CIA Triad** (Confidentiality, Integrity, Availability) is the foundational model used to guide information security policy, risk assessment, and control design. Every security control, from encryption to backups to access lists, exists to protect one or more of these three properties. It is not a protocol or a product — it is the goal framework security professionals measure decisions against.

## 2. Confidentiality

Ensures information is accessible only to those authorized to see it.

**Goal:** Prevent unauthorized disclosure of data.

**Controls:**
- Encryption (at rest and in transit — AES, TLS)
- Access control lists (ACLs) and role-based access control (RBAC)
- Strong authentication (passwords, MFA)
- Data classification and labeling
- Least privilege principle
- Network segmentation, VPNs

**Common threats:** eavesdropping/sniffing, shoulder surfing, stolen credentials, misconfigured cloud storage, social engineering, insider leaks.

**Example:** Encrypting a database of customer records so that even if an attacker steals the disk, the data is unreadable without the key.

## 3. Integrity

Ensures information is accurate, complete, and has not been altered by unauthorized parties.

**Goal:** Prevent unauthorized or accidental modification of data.

**Controls:**
- Hashing (MD5, SHA-256) to detect tampering
- Digital signatures
- Checksums and file integrity monitoring (FIM) tools like Tripwire
- Version control
- Access controls that prevent unauthorized writes
- Audit logging

**Common threats:** man-in-the-middle attacks, malware modifying files, SQL injection altering database records, unauthorized configuration changes.

**Example:** A software vendor publishes a SHA-256 checksum alongside a download so users can verify the file wasn't tampered with in transit.

## 4. Availability

Ensures systems, applications, and data are accessible to authorized users when needed.

**Goal:** Keep services running and data reachable.

**Controls:**
- Redundancy (RAID, failover clusters, redundant power/ISP links)
- Load balancing
- Regular backups and tested disaster recovery (DR) plans
- DDoS protection and rate limiting
- Patch management to avoid crashes from unpatched bugs
- High-availability (HA) architecture

**Common threats:** DDoS attacks, ransomware, hardware failure, power outages, natural disasters, software bugs causing crashes.

**Example:** A web application behind a load balancer with multiple backend servers so a single server failure doesn't cause downtime.

## 5. Balancing the Triad

The three properties often trade off against each other:

| Trade-off | Example |
|---|---|
| Confidentiality vs Availability | Strict access controls can slow down legitimate users needing fast access |
| Integrity vs Availability | Extensive validation/logging adds overhead that can reduce performance |
| Confidentiality vs Integrity | Heavy encryption everywhere can complicate change tracking and auditing |

Good security design finds the right balance for the specific system rather than maximizing one property blindly (e.g., a public news website prioritizes availability and integrity over confidentiality, while a military database prioritizes confidentiality first).

## 6. Beyond the Triad — Related Models

- **Parkerian Hexad** — extends CIA with three more attributes: **Possession/Control**, **Authenticity**, and **Utility**. Used when CIA alone doesn't fully capture a scenario (e.g., stolen encrypted data violates possession even if confidentiality is technically intact).
- **AAA (Authentication, Authorization, Accounting)** — the operational mechanism that enforces CIA in practice (see [[AAA]]).
- **Non-repudiation** — often paired with CIA; ensures a party cannot deny having performed an action (achieved via digital signatures, logs).

## 7. Real-World Mapping of Attacks to CIA

| Attack | Primary CIA Property Violated |
|---|---|
| Data breach / leaked database | Confidentiality |
| Website defacement | Integrity |
| Ransomware encrypting files | Availability (and often Confidentiality via exfiltration) |
| DDoS attack | Availability |
| Man-in-the-middle altering a transaction | Integrity |
| Phishing to steal credentials | Confidentiality |
| SQL injection modifying records | Integrity |

## 8. Interview Questions

1. What does CIA stand for in security? → **Confidentiality, Integrity, Availability**
2. Which CIA property does encryption primarily protect? → **Confidentiality**
3. Which CIA property do hashing and digital signatures protect? → **Integrity**
4. Which CIA property does a DDoS attack target? → **Availability**
5. What extends the CIA triad with Possession, Authenticity, and Utility? → **Parkerian Hexad**
6. Give an example of a control that supports Availability. → **Redundancy / backups / load balancing**

## 9. Key Points

- CIA Triad = **Confidentiality, Integrity, Availability**.
- Confidentiality → prevents unauthorized disclosure (encryption, access control).
- Integrity → prevents unauthorized modification (hashing, signatures).
- Availability → ensures uptime and access (redundancy, backups, DR).
- Every security control maps back to protecting one or more of these three.
- Balancing all three is the core challenge of security architecture.

---
*Related: [[AAA]], [[VPN]]*
