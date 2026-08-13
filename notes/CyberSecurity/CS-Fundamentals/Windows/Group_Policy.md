## 1. Introduction

**Group Policy** is Windows' native, centralized configuration-management system — letting administrators enforce settings (security, software deployment, restrictions) across many machines at once, either locally on a single machine (**Local Group Policy**) or, at scale, across an entire **Active Directory** domain via **Group Policy Objects (GPOs)** (see [[Active_Directory_Basics]]).

## 2. Local vs Domain Group Policy

| Scope | Applies to | Managed via |
|---|---|---|
| Local Group Policy | A single, standalone (non-domain) machine | `gpedit.msc` |
| Domain Group Policy (GPOs) | Any number of domain-joined computers/users, scoped by OU/site/domain | Group Policy Management Console (`gpmc.msc`) on a Domain Controller/admin workstation |

```
gpedit.msc          :: Local Group Policy Editor (not available on Home editions)
gpmc.msc                :: Group Policy Management Console (domain environments)
```

## 3. GPO Structure

A **Group Policy Object** is a collection of settings split into two top-level halves:

| Section | Applies to | Applied when |
|---|---|---|
| Computer Configuration | The machine itself, regardless of who logs in | At boot and periodic refresh |
| User Configuration | The specific logged-in user, regardless of which machine | At logon and periodic refresh |

Within each, settings are organized as:

```
Computer/User Configuration
   ├── Software Settings          (software deployment)
   ├── Windows Settings           (scripts, security settings)
   └── Administrative Templates   (registry-based policy — the vast majority of settings)
```

**Administrative Templates**, in practice, are simply registry values written under specific policy-designated keys (`HKLM\SOFTWARE\Policies\...` and `HKCU\SOFTWARE\Policies\...`) — Group Policy is, underneath, largely a mechanism for pushing registry changes at scale.

## 4. GPO Scope and Application Order

In a domain, GPOs are linked to **Sites**, **Domains**, and **Organizational Units (OUs)**, and applied in a specific order, commonly remembered as **LSDOU**:

```
Local → Site → Domain → OU (and nested OUs, outer to inner)
```

Later-applied policies win on conflicting settings by default (OU-linked GPOs generally take precedence over domain-linked ones), unless **Enforced** (formerly "No Override") is set on a higher-level GPO, or **Block Inheritance** is set on an OU.

| Modifier | Effect |
|---|---|
| Enforced | This GPO's settings cannot be overridden by lower-level GPOs |
| Block Inheritance | An OU refuses to inherit GPOs linked above it (unless those GPOs are Enforced) |
| Security Filtering | Restricts which users/computers a linked GPO actually applies to, via group membership |
| WMI Filtering | Restricts application based on a WMI query result (e.g., only to a specific OS version) |

## 5. Command-Line Tools

```cmd
gpupdate /force                :: reapply Group Policy immediately, ignoring the periodic refresh cycle
gpresult /r                       :: summary of which GPOs applied to the current user/computer
gpresult /h report.html               :: detailed HTML report — invaluable for troubleshooting policy application
```

```powershell
Get-GPO -All                                    # (requires GroupPolicy module, typically on a DC or RSAT-equipped machine)
Get-GPOReport -All -ReportType Html -Path report.html
New-GPO -Name "Restrict USB" | New-GPLink -Target "OU=Finance,DC=corp,DC=local"
```

## 6. Common Security-Relevant GPO Settings

| Setting area | Example |
|---|---|
| Password Policy | Minimum length, complexity, history, max age |
| Account Lockout Policy | Failed attempt threshold, lockout duration |
| User Rights Assignment | Who can log on locally/via RDP, who can debug programs, etc. |
| Windows Firewall | Centralized firewall rule deployment |
| Software Restriction Policies / AppLocker | Application allow/deny lists (see [[Windows_Security_Features]]) |
| Audit Policy | Which events get logged (see [[Event_Log_Management]]) |
| Windows Update | Deferral, WSUS server pointer, reboot behavior |

## 7. Security Relevance — GPOs as an Attack and Defense Surface

| Angle | Detail |
|---|---|
| GPO-based lateral movement/persistence | An attacker with domain admin (or GPO-edit) rights can push a malicious logon script, scheduled task, or registry change to an entire OU via a single GPO edit — a highly efficient way to compromise many machines at once |
| GPO permission misconfiguration | If a low-privileged user/group has Edit rights on a GPO linked to a sensitive OU (e.g., Domain Controllers), that's a direct path to domain-wide compromise — a well-known AD attack path, often surfaced by tools like BloodHound |
| SYSVOL and Group Policy Preferences (GPP) passwords | Historically, GPP allowed storing local account passwords in GPO XML files under the domain's SYSVOL share, encrypted with a since-published static AES key — patched (MS14-025), but old GPP-cached credentials are still occasionally found during assessments |

```powershell
# Look for legacy GPP credential XML files (historical finding, still worth checking)
Get-ChildItem \\domain.local\SYSVOL -Recurse -Include *.xml -ErrorAction SilentlyContinue |
    Select-String -Pattern "cpassword"
```

## 8. Interview Questions

1. What does "LSDOU" describe? → **The order Group Policy is applied in a domain: Local → Site → Domain → Organizational Unit (outer to inner), with later-applied settings generally winning on conflicts**
2. What's the difference between "Enforced" and "Block Inheritance"? → **Enforced (on a GPO) prevents lower-level GPOs from overriding its settings; Block Inheritance (on an OU) refuses inherited GPOs from above unless they're Enforced**
3. What are Administrative Templates, mechanically? → **Registry-based policy settings — GP applies them by writing values under `HKLM\SOFTWARE\Policies` / `HKCU\SOFTWARE\Policies`**
4. Why is GPO edit permission on a sensitive OU (like Domain Controllers) such a serious finding in an AD security assessment? → **Anyone with Edit rights on that GPO can push arbitrary logon scripts, scheduled tasks, or settings to every computer/user in scope — effectively domain-wide compromise without needing Domain Admin credentials directly**
5. What historical Group Policy feature leaked recoverable local account passwords, and how? → **Group Policy Preferences (GPP) stored local account passwords in SYSVOL-hosted XML files, encrypted with a static AES key Microsoft had published — patched via MS14-025, but old cached files are still sometimes found**

## 9. Key Points

- Group Policy splits into **Computer Configuration** (machine-wide) and **User Configuration** (per logged-in user), applied via **LSDOU** order.
- Administrative Templates are, underneath, mostly a mechanism for centrally pushing **registry values**.
- `gpupdate /force` and `gpresult /r` (or `/h`) are the essential command-line tools for applying and troubleshooting policy.
- Misconfigured **GPO edit permissions** on sensitive OUs are a top-tier Active Directory privilege-escalation/lateral-movement path — a frequent finding in real assessments.
- Legacy **GPP cpassword** exposure is a well-known (now-patched) historical vulnerability class still worth checking for.

---
*Related: [[Windows_Registry]], [[Active_Directory_Basics]], [[Windows_Security_Features]], [[Windows_Privilege_Escalation]]*
