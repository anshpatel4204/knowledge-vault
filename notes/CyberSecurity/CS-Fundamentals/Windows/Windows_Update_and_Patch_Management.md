## 1. Introduction

Patch management is one of the highest-leverage security controls in any Windows environment — the overwhelming majority of real-world exploited vulnerabilities are for flaws with a patch already available at the time of compromise. Understanding how Windows Update actually works, and how it's managed at enterprise scale, is core to both system administration and vulnerability management practice.

## 2. Windows Update — Client Side

```
Settings → Update & Security → Windows Update
```

```powershell
Get-HotFix                             # list installed updates (KB numbers)
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10

# Requires the PSWindowsUpdate module (not built-in)
Install-Module PSWindowsUpdate
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot
```

```cmd
wmic qfe list brief                       :: legacy way to list installed patches (Quick Fix Engineering)
```

## 3. Update Types

| Update type | Cadence | Purpose |
|---|---|---|
| Security updates | Monthly ("Patch Tuesday" — second Tuesday of each month) | Fixes for CVEs |
| Cumulative updates | Monthly | Bundles all prior fixes for a given OS version |
| Feature updates | 1-2x/year | New OS version/major feature releases |
| Out-of-band updates | As needed | Emergency fixes for actively-exploited critical vulnerabilities, outside the normal cadence |
| Driver updates | As available | Hardware driver patches, sometimes delivered via Windows Update |

## 4. KB Numbers and CVEs

Every update is identified by a **KB (Knowledge Base) article number** (e.g., KB5034123). A single KB update typically bundles fixes for multiple **CVEs (Common Vulnerabilities and Exposures)** — the security-relevant question during vulnerability management is usually "which CVEs does this KB fix, and is my fleet's patch level current against them."

```powershell
Get-HotFix -Id KB5034123        # check whether a specific KB is installed
```

## 5. Enterprise Patch Management — WSUS and SCCM/Intune

Large organizations don't let every machine pull updates directly from Microsoft — they stage and control rollout centrally:

| Tool | Role |
|---|---|
| **WSUS (Windows Server Update Services)** | On-premises server that downloads updates once, then distributes/approves them for internal machines — admins can test before wide rollout |
| **SCCM (Microsoft Configuration Manager)** | Full endpoint management suite, including patch deployment, software distribution, compliance reporting |
| **Microsoft Intune** | Cloud-based (Modern/MDM) device management, including update rings for phased rollout |
| **Group Policy** | Can point clients at a WSUS server and control update behavior (deferral, reboot behavior) |

```
Group Policy: Computer Configuration → Administrative Templates → Windows Components → Windows Update
```

## 6. Why Patch Management Is a Security Priority

| Reason | Explanation |
|---|---|
| Exploit weaponization is fast | Public exploits for disclosed CVEs often appear within days, sometimes hours |
| Patch gap window | The time between patch release and full fleet deployment is the highest-risk period — attackers specifically target this window ("n-day" exploitation) |
| EternalBlue / WannaCry precedent | The MS17-010 SMB vulnerability had a patch available a month before WannaCry's global outbreak — a textbook case of patch-gap exploitation |
| Zero-days vs n-days | Zero-days (no patch exists yet) get headlines, but the vast majority of real breaches exploit **already-patched, known vulnerabilities** on unpatched systems |

## 7. Vulnerability Scanning and Patch Compliance

Organizations pair patch management with vulnerability scanners (Nessus, Qualys, OpenVAS) to continuously verify patch compliance against known CVEs — a core part of the broader **Vulnerability Management** discipline (see [[Vulnerability_Management]]).

```powershell
# Quick local check for missing updates (via Windows Update Agent COM API)
$Session = New-Object -ComObject Microsoft.Update.Session
$Searcher = $Session.CreateUpdateSearcher()
$Result = $Searcher.Search("IsInstalled=0")
$Result.Updates | Select-Object Title
```

## 8. Interview Questions

1. What day of the month is "Patch Tuesday," and why does the term matter? → **The second Tuesday of each month — Microsoft's standard monthly security update release, giving admins a predictable patching cadence to plan around**
2. What's the difference between a security update and a cumulative update? → **A security update fixes specific CVEs; a cumulative update bundles all prior fixes (security and non-security) for that OS version into one package**
3. What is WSUS, and why do enterprises use it instead of letting machines update directly from Microsoft? → **Windows Server Update Services — an on-prem server that downloads updates once and lets admins test/approve/stage rollout centrally, rather than every machine pulling independently**
4. What real-world incident is the textbook example of "patch gap" exploitation? → **WannaCry — it exploited the MS17-010 SMB vulnerability, for which Microsoft had already released a patch about a month before the outbreak**
5. Why do most real breaches exploit "n-day" rather than zero-day vulnerabilities? → **Patches for known CVEs exist but haven't been applied everywhere yet — attackers exploit that lag, which is far cheaper and more reliable than finding/weaponizing a true zero-day**

## 9. Key Points

- Updates are grouped by type (security, cumulative, feature, out-of-band) and tracked by **KB numbers**, which map to one or more **CVEs**.
- Enterprises manage patching centrally via **WSUS**, **SCCM**, or **Intune**, rather than direct-from-Microsoft updates, to control rollout risk.
- The **patch gap** — time between patch release and full deployment — is the highest-risk window and the one attackers most reliably exploit ("n-day" attacks), as demonstrated by WannaCry/MS17-010.
- Patch management is inseparable from **vulnerability management** — scanning verifies what patch management should have already fixed.

---
*Related: [[Windows_Security_Features]], [[Vulnerability_Management]], [[Windows_Privilege_Escalation]]*
