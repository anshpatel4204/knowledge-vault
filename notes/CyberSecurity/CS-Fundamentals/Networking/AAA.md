## 1. Introduction

**AAA** stands for **Authentication, Authorization, and Accounting** — the three processes that control and track access to a network or system. Where the CIA triad defines *what* security should achieve, AAA defines the *mechanism* that enforces it at the point of access. AAA is implemented by protocols such as **RADIUS**, **TACACS+**, and **Kerberos**, and by servers/directories such as Active Directory and LDAP.

## 2. Authentication

**"Who are you?"** — Verifying the identity of a user, device, or system before granting access.

**Factors of authentication:**

| Factor | Category | Example |
|---|---|---|
| Something you know | Knowledge | Password, PIN |
| Something you have | Possession | OTP token, smart card, phone |
| Something you are | Inherence | Fingerprint, face/iris scan |
| Somewhere you are | Location | GPS, IP geolocation |
| Something you do | Behavior | Typing pattern, gait |

**Multi-Factor Authentication (MFA):** combines two or more factors (e.g., password + OTP) to significantly reduce the risk of compromised credentials.

**Common authentication methods:** username/password, certificates (PKI), biometrics, tokens (hardware/software), Kerberos tickets, SSO (Single Sign-On).

## 3. Authorization

**"What are you allowed to do?"** — Determining what an authenticated identity is permitted to access or perform, enforced *after* authentication succeeds.

**Common models:**

| Model | Description |
|---|---|
| DAC (Discretionary Access Control) | Resource owner decides who gets access |
| MAC (Mandatory Access Control) | System-enforced labels/clearances (e.g., SELinux, military systems) |
| RBAC (Role-Based Access Control) | Permissions assigned to roles, users assigned to roles |
| ABAC (Attribute-Based Access Control) | Access decided by attributes (user, resource, environment) |
| Rule-Based Access Control | Access governed by explicit rules (e.g., firewall ACLs) |

**Principle of Least Privilege:** grant only the minimum access necessary to perform a task.

## 4. Accounting

**"What did you do?"** — Logging and tracking user activity for auditing, billing, forensics, and compliance.

**Typically recorded:**
- Login/logout timestamps
- Commands executed / resources accessed
- Data transferred
- Session duration
- Source IP / device

**Uses:** intrusion detection, forensic investigation, compliance (PCI-DSS, HIPAA), usage-based billing (ISPs).

## 5. AAA Protocols

### RADIUS (Remote Authentication Dial-In User Service)
- UDP-based (ports **1812** authentication/authorization, **1813** accounting; legacy 1645/1646).
- Combines authentication and authorization into a single response.
- Encrypts only the password in the packet, not the entire payload.
- Widely used for network access — Wi-Fi (802.1X), VPN, ISP dial-up.

### TACACS+ (Terminal Access Controller Access-Control System Plus)
- Cisco-developed, TCP-based (port **49**).
- Encrypts the **entire packet body**, not just the password.
- Separates authentication, authorization, and accounting into distinct processes — allows granular per-command authorization.
- Commonly used for **device administration** (router/switch CLI access).

### Kerberos
- Ticket-based authentication protocol (port **88**), developed at MIT.
- Uses a trusted third party — the **Key Distribution Center (KDC)** — with two components: the **Authentication Server (AS)** and **Ticket Granting Server (TGS)**.
- Issues time-limited tickets so passwords are never sent over the network after initial login.
- Backbone of authentication in Microsoft Active Directory domains.

### LDAP (Lightweight Directory Access Protocol)
- Port **389** (plaintext) / **636** (LDAPS, encrypted).
- Not strictly an AAA protocol, but a directory service protocol often used to store identities that AAA systems authenticate against.

## 6. RADIUS vs TACACS+

| Feature | RADIUS | TACACS+ |
|---|---|---|
| Transport | UDP | TCP |
| Port | 1812/1813 | 49 |
| Encryption | Password only | Entire packet |
| AAA separation | Combines AuthN + AuthZ | Separates all three |
| Primary use | Network access (Wi-Fi, VPN) | Device administration |
| Vendor | Open standard (RFC 2865) | Cisco proprietary (open-sourced) |

## 7. AAA in Practice — Example Flow

1. A network admin connects to a router via SSH.
2. **Authentication:** Router forwards credentials to a TACACS+ server; server verifies identity.
3. **Authorization:** TACACS+ server returns which commands this admin is allowed to run (e.g., `show` commands only, not `configure terminal`).
4. **Accounting:** Every command the admin runs is logged with timestamp and session ID to the TACACS+ server for audit purposes.

## 8. Interview Questions

1. What does AAA stand for? → **Authentication, Authorization, Accounting**
2. Which protocol encrypts the entire packet — RADIUS or TACACS+? → **TACACS+**
3. Which transport protocol does RADIUS use? → **UDP**
4. What does Kerberos use to avoid sending passwords repeatedly? → **Tickets**
5. What is the principle of least privilege related to? → **Authorization**
6. Which AAA process is used for forensic investigation of past activity? → **Accounting**

## 9. Key Points

- AAA = **Authentication → Authorization → Accounting**, always in that order.
- Authentication verifies identity; Authorization defines permissions; Accounting logs activity.
- **RADIUS** (UDP, network access) and **TACACS+** (TCP, device admin) are the two dominant AAA protocols.
- **Kerberos** uses tickets and a KDC to avoid repeated password transmission.
- MFA strengthens authentication by combining multiple factor categories.

---
*Related: [[CIA_Triad]], [[VPN]]*
