## 1. Introduction

Windows authentication determines how a user or machine proves its identity — locally via the **SAM** database, or across a network via **NTLM** or **Kerberos**. The process `lsass.exe` (Local Security Authority Subsystem Service) sits at the center of nearly all of it, which is exactly why LSASS is one of the single most commonly targeted processes in Windows-based attacks.

## 2. Local Authentication — SAM and LSASS

| Component | Role |
|---|---|
| SAM (Security Account Manager) | Database of local user accounts and their **NTLM password hashes**, stored in the `HKLM\SAM` registry hive |
| LSASS (`lsass.exe`) | Enforces the local security policy, handles logon, and — critically — **caches credentials and hashes in memory** for active sessions |
| LSA (Local Security Authority) | The broader security subsystem LSASS implements |

Because Windows never stores plaintext passwords, only their **hashes**, an attacker who dumps the SAM database or LSASS memory gets the hashes, not the original password — but on Windows, that's often enough (see **Pass-the-Hash** below).

```cmd
:: (Requires SYSTEM/admin) dump the SAM hive for offline hash extraction
reg save HKLM\SAM sam.save
reg save HKLM\SYSTEM system.save
```

## 3. NTLM Authentication

**NTLM (NT LAN Manager)** is Microsoft's legacy challenge-response authentication protocol, still supported today for backward compatibility (workgroup environments, IP-address-based access, older applications).

```
Client → Server: "I want to authenticate as Ansh"
Server → Client: sends a random CHALLENGE
Client → Server: sends CHALLENGE encrypted with the NTLM hash of Ansh's password (the RESPONSE)
Server → (Domain Controller, if domain account): verifies the response is correct
```

| Weakness | Consequence |
|---|---|
| No mutual authentication | Client can't verify the server's identity — enables NTLM relay attacks |
| Hash itself is often sufficient | Pass-the-Hash — an attacker with the NTLM hash can authenticate without ever knowing the plaintext password |
| Vulnerable to relay | An attacker positioned in the network can relay a captured NTLM authentication attempt to a different server, authenticating *as* the victim |

**NTLM relay** and **Pass-the-Hash** are two of the most common lateral-movement techniques in Windows/AD environments (see [[Windows_Persistence_and_Lateral_Movement]]).

## 4. Kerberos Authentication

**Kerberos** is the modern, default authentication protocol for Active Directory domains — a ticket-based system that avoids sending passwords or hashes over the network at all after initial logon.

```
1. Client → KDC (AS-REQ): request a Ticket Granting Ticket (TGT), proving identity with a timestamp encrypted using the user's key
2. KDC → Client (AS-REP): issues a TGT, encrypted with the krbtgt account's key
3. Client → KDC (TGS-REQ): presents the TGT, requests a service ticket for a specific resource (e.g., a file server)
4. KDC → Client (TGS-REP): issues a Service Ticket, encrypted with that service's key
5. Client → Server (AP-REQ): presents the Service Ticket directly to the target server — no KDC round-trip needed
```

| Term | Meaning |
|---|---|
| KDC (Key Distribution Center) | Runs on every Domain Controller — issues tickets |
| TGT (Ticket Granting Ticket) | Proof of authentication, used to request further service tickets |
| Service Ticket (TGS) | Grants access to one specific service, encrypted with that service's own key |
| krbtgt account | Special AD account whose key encrypts all TGTs — compromise of its hash enables **Golden Ticket** attacks |

## 5. Kerberos Attacks — Why It Matters for Security

| Attack | Description |
|---|---|
| **Kerberoasting** | Request service tickets for accounts with a Service Principal Name (SPN), then crack the ticket offline to recover the service account's password — works because service tickets are encrypted with the service account's own (often weak) password hash |
| **AS-REP Roasting** | Targets accounts with Kerberos pre-authentication disabled — allows requesting an encrypted AS-REP without any credentials, then cracking it offline |
| **Golden Ticket** | A forged TGT built using a stolen **krbtgt** hash — grants domain-wide access as any user, valid until the krbtgt password is rotated (twice) |
| **Silver Ticket** | A forged Service Ticket for one specific service, built using that service account's hash — narrower but stealthier than a Golden Ticket |
| **Pass-the-Ticket** | Reusing a stolen Kerberos ticket (TGT or service ticket) from another session, rather than authenticating fresh |

## 6. NTLM vs Kerberos — Comparison

| Aspect | NTLM | Kerberos |
|---|---|---|
| Model | Challenge-response | Ticket-based |
| Mutual authentication | No | Yes |
| Requires a Domain Controller | No (works in workgroups) | Yes (needs a KDC) |
| Password/hash sent over the network | Response derived from hash, sent each time | Never sent after initial TGT issuance |
| Default in modern AD | Fallback only | Default |
| Common attacks | Relay, Pass-the-Hash | Kerberoasting, Golden/Silver Ticket, Pass-the-Ticket |

## 7. LSASS as an Attack Target

Because LSASS holds cached credentials, NTLM hashes, and Kerberos tickets **in memory** for active sessions, dumping its memory is one of the highest-value actions in a Windows post-exploitation chain.

```
Tools historically used: mimikatz, Task Manager "Create dump file", comsvcs.dll MiniDump, procdump
```

**Defenses:**

| Defense | How it helps |
|---|---|
| Credential Guard | Isolates LSASS secrets in a hardware-virtualized, protected memory region even SYSTEM-level access can't read directly (see [[Windows_Security_Features]]) |
| Protected Process Light (PPL) for LSASS | Restricts which processes are even allowed to open a handle to `lsass.exe` |
| Restricting Domain Admin logons | Reducing where highly-privileged credentials get cached at all |

## 8. Interview Questions

1. What does Windows actually store for a local password — the plaintext, or something else? → **Only the NTLM hash, stored in the SAM database (`HKLM\SAM`)**
2. Why is Pass-the-Hash possible on Windows? → **NTLM authentication only requires the password's hash, not the plaintext, so an attacker who obtains the hash can authenticate without ever cracking the actual password**
3. What is Kerberoasting, and why does it work? → **Requesting service tickets for SPN-associated accounts and cracking them offline — it works because service tickets are encrypted with the target service account's own password hash, which may be weak**
4. What makes a Golden Ticket attack so severe? → **It's a forged TGT built from the krbtgt account's hash, granting domain-wide access as any user, and remains valid until the krbtgt password is rotated (twice, due to password history)**
5. What Windows security feature specifically protects LSASS memory from credential dumping? → **Credential Guard, which isolates secrets in a hardware-virtualized protected memory region**

## 9. Key Points

- **LSASS** is the central authentication process — it caches credentials/hashes/tickets in memory, making it a top post-exploitation target.
- **NTLM** is a legacy challenge-response protocol vulnerable to relay and Pass-the-Hash; **Kerberos** is the modern, ticket-based AD default, avoiding hash transmission after initial logon.
- **Kerberoasting**, **Golden/Silver Tickets**, and **Pass-the-Ticket** are the signature Kerberos-specific attacks, all covered further in [[Windows_Persistence_and_Lateral_Movement]].
- **Credential Guard** and **LSASS Protected Process Light** are the primary modern defenses against credential dumping.

---
*Related: [[Users_Groups_and_SID]], [[Active_Directory_Basics]], [[Windows_Security_Features]], [[Windows_Persistence_and_Lateral_Movement]]*
