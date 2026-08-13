## 1. Introduction

**Active Directory (AD)** is Microsoft's directory service for Windows domain networks — a centralized database and set of services managing users, computers, groups, and policy for an entire organization. It is, without exaggeration, the backbone of enterprise Windows security: the majority of real-world internal penetration tests and enterprise breaches ultimately revolve around obtaining, escalating, or abusing AD privileges.

## 2. Core Concepts

| Term | Meaning |
|---|---|
| Domain | A logical grouping of users/computers sharing a common AD database and security policy |
| Domain Controller (DC) | A server running AD Domain Services (AD DS), holding a writable copy of the domain database, handling authentication (Kerberos KDC — see [[Windows_Authentication]]) |
| Forest | The top-level AD container — one or more domains sharing a common schema and Global Catalog |
| Tree | A group of domains within a forest sharing a contiguous DNS namespace |
| Organizational Unit (OU) | A container used to organize objects (users, computers, groups) for delegated administration and Group Policy application (see [[Group_Policy]]) |
| Schema | Defines what object types (and their attributes) can exist in AD |
| Global Catalog | A partial, searchable index of every object across an entire forest, for fast cross-domain lookups |

```
Forest
 └── Tree (contiguous DNS namespace)
      └── Domain (corp.local)
           ├── OU: Finance
           │     ├── User objects
           │     └── Computer objects
           └── OU: IT
                 └── Group objects
```

## 3. AD Objects

| Object type | Represents |
|---|---|
| User | A person's (or service's) account |
| Computer | A domain-joined machine, itself an authenticated security principal with its own account/password |
| Group | A collection of users/computers/other groups, used for permission assignment |
| GPO | A Group Policy Object (linked to an OU/domain/site) |
| Contact | A non-security object (e.g., an external email contact, no login rights) |

Every AD object has a **Distinguished Name (DN)** identifying its exact location in the hierarchy:

```
CN=Ansh Patel,OU=Finance,DC=corp,DC=local
```

## 4. Group Types and Scopes

| Group type | Purpose |
|---|---|
| Security group | Used for permission assignment (ACLs) and Group Policy filtering |
| Distribution group | Email distribution only — no security capability |

| Group scope | Membership can include | Usable for permissions in |
|---|---|---|
| Domain Local | Users/groups from any domain in the forest | Only the local domain |
| Global | Users/groups from the same domain only | Any domain in the forest (with a trust) |
| Universal | Users/groups from any domain in the forest | Any domain in the forest |

## 5. LDAP — The Protocol Underneath

AD is queried and managed via **LDAP (Lightweight Directory Access Protocol)**, typically on port 389 (or 636 for LDAPS/encrypted). Nearly every AD administration tool — including built-in ones — is an LDAP client under the hood.

```powershell
Import-Module ActiveDirectory
Get-ADUser -Filter *
Get-ADUser -Identity ansh -Properties *
Get-ADGroupMember -Identity "Domain Admins"
Get-ADComputer -Filter * | Select-Object Name, OperatingSystem
```

```cmd
dsquery user -name "Ansh*"                :: legacy LDAP query tool
```

## 6. Domain Controllers and Replication

| Concept | Meaning |
|---|---|
| Multi-master replication | Every writable DC holds a full, writable copy of the domain database, replicating changes to all other DCs |
| FSMO roles | Five special, single-master operations not suited to multi-master replication (e.g., Schema Master, PDC Emulator) — each held by exactly one DC at a time |
| RODC (Read-Only Domain Controller) | Holds a read-only AD copy, useful for less-trusted/branch-office locations |
| SYSVOL | A shared folder on every DC replicating Group Policy objects/scripts to all domain members |

## 7. Trusts

A **trust** allows users in one domain (or forest) to authenticate against resources in another:

| Trust type | Description |
|---|---|
| Parent-child (within a forest) | Automatic, two-way transitive trust between domains in the same tree/forest |
| Forest trust | Explicit trust between two entirely separate forests |
| External trust | Non-transitive trust to a domain outside the forest, or to a legacy NT domain |

**Security relevance:** trust relationships are a major attack-surface consideration — compromising a less-secure trusted domain/forest can provide a path into a more sensitive trusting one, and this "attack path" style thinking (which tools like **BloodHound** automate by mapping AD relationships graphically) is central to modern AD security assessment.

## 8. Why AD Compromise Is So Consequential

| Reason | Explanation |
|---|---|
| Single source of truth | Nearly every account, permission, and policy decision across the enterprise traces back to AD |
| krbtgt account | Compromise of this single account's hash enables domain-wide Golden Ticket forgery (see [[Windows_Authentication]]) |
| Domain Admins group | Membership grants effectively unrestricted control over every domain-joined resource |
| Attack path density | Real AD environments frequently contain long, non-obvious privilege-escalation chains (nested group memberships, GPO edit rights, delegation misconfigurations) that tools like BloodHound are specifically built to surface |

## 9. Interview Questions

1. What's the difference between an AD forest, tree, and domain? → **A domain is a single administrative/security boundary; a tree is domains sharing a contiguous DNS namespace; a forest is the top-level boundary, one or more trees sharing a common schema and Global Catalog**
2. What protocol underlies AD queries and administration? → **LDAP (Lightweight Directory Access Protocol)**
3. What's the difference between a security group and a distribution group? → **Security groups can be used for permission assignment (ACLs) and GPO filtering; distribution groups are for email only and have no security function**
4. What is SYSVOL, and what does it replicate? → **A shared folder present on every Domain Controller, replicating Group Policy Objects and logon/startup scripts to all domain members**
5. Why is the krbtgt account so security-critical? → **Its password hash encrypts every Kerberos TGT issued in the domain; compromising it enables Golden Ticket forgery, granting domain-wide access as any user**

## 10. Key Points

- AD organizes objects (**users, computers, groups**) into **OUs**, within **domains**, within **trees**, within a **forest** — the largest AD administrative boundary.
- **LDAP** is the underlying protocol; **Kerberos** (via DCs acting as KDCs) is the underlying authentication protocol (see [[Windows_Authentication]]).
- **Domain Controllers** hold the writable directory database and replicate multi-master, except for five single-owner **FSMO roles**.
- **Trusts** extend authentication across domains/forests and are a key attack-path consideration.
- AD's centrality makes it the single highest-value target in most enterprise Windows environments — understanding it is prerequisite to understanding [[Windows_Persistence_and_Lateral_Movement]] and most real-world Windows privilege escalation.

---
*Related: [[Windows_Authentication]], [[Group_Policy]], [[Domain_vs_Workgroup]], [[Windows_Persistence_and_Lateral_Movement]]*
