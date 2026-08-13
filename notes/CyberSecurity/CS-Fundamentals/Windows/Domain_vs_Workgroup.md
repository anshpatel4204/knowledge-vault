## 1. Introduction

Every Windows machine belongs to either a **Workgroup** (peer-to-peer, no central authority) or a **Domain** (centrally managed via Active Directory). This single distinction fundamentally changes how authentication, policy, and trust work across a network — and recognizing which model a target environment uses is one of the first things both administrators and attackers assess.

## 2. Workgroup Model

| Characteristic | Detail |
|---|---|
| Structure | Peer-to-peer — no central server, no central authority |
| Authentication | Each machine maintains its own local **SAM** database (see [[Windows_Authentication]]) — there's no shared identity |
| Typical scale | Home networks, very small offices (informally, a practical ceiling around 10-20 machines) |
| Account management | Manual, per-machine — creating "Ansh" on Machine A and "Ansh" on Machine B creates two entirely separate accounts with (potentially) different passwords and different SIDs |
| Policy | Only **Local Group Policy**, configured per-machine individually |

```cmd
systeminfo | findstr /B "Domain"       :: check if a machine is domain-joined or in a workgroup
```

## 3. Domain Model

| Characteristic | Detail |
|---|---|
| Structure | Centralized — one or more Domain Controllers hold the authoritative Active Directory database |
| Authentication | Centralized via **Kerberos** (with NTLM fallback) against the domain database — one account works everywhere in the domain |
| Typical scale | Anywhere from dozens to hundreds of thousands of machines |
| Account management | Centralized — a single AD user object grants access across every domain-joined resource the account is permitted to touch |
| Policy | **Domain Group Policy (GPOs)**, centrally managed and pushed to every domain member (see [[Group_Policy]]) |

```cmd
net user /domain                          :: (from a domain-joined machine) query domain user accounts
whoami /fqdn                                 :: shows domain-qualified identity if domain-joined
```

## 4. Joining a Domain — What Actually Changes

When a machine joins a domain, it becomes a **computer object** in AD in its own right, with its own account/password (rotated automatically), and gains:

- Trust in the domain's Kerberos realm for authentication
- The ability for domain accounts (not just local ones) to log on
- Applicability to domain **Group Policy Objects**
- A relationship to a Domain Controller for authentication and policy retrieval (the **secure channel**, protected by the computer account's own credentials)

```powershell
Add-Computer -DomainName "corp.local" -Credential (Get-Credential) -Restart
Get-ComputerInfo | Select-Object CsDomain, CsPartOfDomain
```

## 5. Local Accounts Still Exist on Domain-Joined Machines

Even on a domain-joined machine, the local **SAM** database and local **Administrator** account still exist and can still be used — this matters a great deal for security:

| Scenario | Relevance |
|---|---|
| Local admin password reuse | If the same local Administrator password is reused across many machines (common in poorly-managed environments), compromising it on one machine compromises all of them — this exact pattern is what **LAPS (Local Administrator Password Solution)** was built to prevent, by randomizing and centrally managing each machine's local admin password |
| Local account lateral movement | Attackers pivot machine-to-machine using shared local credentials, entirely independent of any domain account compromise |
| RID 500 still applies | The local Administrator's SID still ends in `-500` on every domain-joined machine too (see [[Users_Groups_and_SID]]) |

## 6. Authentication Flow Comparison

| Step | Workgroup | Domain |
|---|---|---|
| Logon target | The local machine's own SAM database | A Domain Controller (Kerberos KDC) |
| Credential validity | Only on that one machine | Across every domain resource the account is permitted to access |
| Network resource access | Requires separate credentials per machine (or matching local username/password, relying on NTLM) | Single sign-on across the domain via Kerberos tickets |

## 7. Security Implications of Each Model

| Model | Security characteristic |
|---|---|
| Workgroup | Smaller blast radius per compromised account (limited to one machine), but harder to centrally enforce policy, patch, or audit consistently |
| Domain | Centralized policy/patching/auditing (a real advantage), but a much larger blast radius — compromising the right domain account (or the domain itself) can cascade to every joined machine, which is exactly why AD security (see [[Active_Directory_Basics]]) receives so much attention |

## 8. Interview Questions

1. What's the fundamental structural difference between a workgroup and a domain? → **A workgroup is peer-to-peer with each machine maintaining its own separate local account database; a domain is centrally managed, with one shared Active Directory database authenticating across every joined machine**
2. If you create a local account named "Ansh" on two different workgroup machines, are they the same account? → **No — they're entirely separate accounts with different SIDs, even if the username and password happen to match**
3. Why does the local Administrator account still matter on a domain-joined machine? → **The local SAM database and local Administrator account still exist independently of the domain; reused local admin passwords across machines are a classic lateral-movement vector, which LAPS is designed to prevent**
4. What happens to a computer's identity when it joins a domain? → **It becomes an AD computer object with its own automatically-rotated account/password, gains Kerberos trust with the domain, and becomes subject to domain Group Policy**
5. Why does the domain model have a larger security "blast radius" than a workgroup, despite being easier to administer centrally? → **A single compromised domain account (or the domain itself) can potentially grant access across every domain-joined machine and resource, whereas workgroup account compromise is generally confined to one machine**

## 9. Key Points

- **Workgroup** = peer-to-peer, per-machine local SAM accounts, Local Group Policy only; **Domain** = centralized AD database, Kerberos-based single sign-on, centrally-pushed Group Policy.
- Domain-joined machines still retain a **local SAM/Administrator account**, and reused local admin passwords across a fleet remain a real, common lateral-movement vector — **LAPS** is the standard mitigation.
- The domain model trades a **larger blast radius** for **centralized administration**, which is precisely why Active Directory security is such a high-priority concern in enterprise environments.

---
*Related: [[Windows_Authentication]], [[Active_Directory_Basics]], [[Users_Groups_and_SID]], [[Group_Policy]]*
