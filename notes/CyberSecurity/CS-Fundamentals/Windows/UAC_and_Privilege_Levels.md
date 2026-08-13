## 1. Introduction

**User Account Control (UAC)**, introduced in Windows Vista, is the mechanism that lets administrators run with standard-user privileges most of the time, elevating only when explicitly needed — reducing the "always-admin" exposure that made older Windows versions such an easy malware target. Understanding UAC requires understanding the underlying **access token** and **integrity level** model it's built on.

## 2. Access Tokens

Every process and thread runs with a **security token** — an object describing the security context (user SID, group memberships, privileges) used for every access-control decision that process makes.

```powershell
whoami /priv                # privileges in the current token
whoami /groups                 # group SIDs in the current token
```

**Admin Approval Mode:** when a user in the Administrators group logs on, Windows actually creates **two** tokens:

| Token | Privilege level |
|---|---|
| Filtered/Standard token | Used by default for everything — admin-group membership present but disabled |
| Full/Elevated token | Only used when a process is explicitly elevated ("Run as administrator") |

This is why even logged-in administrators run most applications (Explorer, browsers) at standard-user rights by default — the elevated token only comes into play on demand.

## 3. Integrity Levels

Beyond the token's user/group identity, Windows adds a separate axis: **Mandatory Integrity Control (MIC)**, assigning every process an **integrity level** that restricts what lower-integrity processes can do to higher-integrity ones, even between processes running as the *same user*.

| Integrity level | Typical use |
|---|---|
| System | SYSTEM processes, kernel-related |
| High | Elevated/admin processes (after UAC prompt) |
| Medium | Standard, non-elevated user processes (the default) |
| Low | Sandboxed/restricted processes (e.g., Protected Mode Internet Explorer, some browser sandboxes) |
| Untrusted | Maximally restricted, minimal access |

```cmd
icacls file.txt                 :: integrity labels (if set) appear alongside normal ACL output
```

A Medium-integrity process generally **cannot** write to, inject into, or otherwise manipulate a High-integrity process, even if both run as the exact same user — this is what actually blocks a compromised standard-integrity browser process from directly tampering with an elevated admin process without a separate exploit.

## 4. UAC Prompt Flow

```
Standard user attempts a privileged action (e.g., installing software, changing system settings)
   → UAC prompt appears on the Secure Desktop (isolated from normal desktop processes)
      → Standard user: must supply admin credentials
      → Administrator (Admin Approval Mode): simply confirms "Yes"
         → Action proceeds with an elevated (High-integrity) token
```

The **Secure Desktop** is a deliberate anti-spoofing measure — it runs in a separate, isolated session that ordinary user-mode malware cannot draw over or interact with, preventing fake "Allow" button spoofing.

## 5. UAC Settings

```
Control Panel → User Accounts → Change User Account Control settings
```

| Level | Behavior |
|---|---|
| Always notify | Prompts for both system changes and app installs, dims the desktop (Secure Desktop) |
| Notify only for app changes (default) | Doesn't prompt for changes the user makes to Windows settings directly |
| Notify without dimming desktop | Same as above, but skips the Secure Desktop (weaker) |
| Never notify | Effectively disables UAC — administrators run everything at full elevation always (not recommended) |

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name EnableLUA
```

## 6. UAC Bypass — A Well-Known Attack Category

UAC is explicitly **not** considered a hard security boundary by Microsoft — it's a convenience/defense-in-depth feature, and numerous "auto-elevate" mechanisms exist for trusted Microsoft binaries (so common admin tools don't constantly prompt). Attackers who already have code execution as a standard-integrity admin-group user can abuse these auto-elevating binaries to silently reach High integrity **without a UAC prompt ever appearing**.

| Technique category | Idea |
|---|---|
| Auto-elevate binary abuse | Certain signed Microsoft binaries (marked `autoElevate` in their manifest) elevate silently — hijacking their DLL search order or registry-driven behavior smuggles attacker code into that elevated context |
| DLL hijacking of elevated processes | Placing a malicious DLL where an auto-elevating process will load it |
| Registry manipulation (`fodhelper.exe`-style techniques) | Abusing how certain trusted binaries consult user-writable registry paths before launching a handler |

**Important nuance:** a UAC bypass only matters if the attacker is already running as a member of the Administrators group at Medium integrity — it elevates Medium→High within the *same* already-privileged account, it does **not** cross from a genuinely unprivileged standard user to admin.

## 7. UAC vs Real Privilege Escalation

| Scenario | Is it "privilege escalation"? |
|---|---|
| Standard non-admin user becomes admin/SYSTEM | Yes — a genuine security boundary crossed (see [[Windows_Privilege_Escalation]]) |
| Admin-group user's Medium-integrity process becomes High-integrity (UAC bypass) | Debatable/limited — same account, same ultimate privilege ceiling, just skips a prompt |

## 8. Interview Questions

1. Why does a logged-in local administrator still run most applications with standard-user rights? → **Admin Approval Mode creates two tokens at logon — a filtered/standard token used by default, and a full/elevated token only invoked on explicit elevation**
2. What is Mandatory Integrity Control, and why does it matter even between processes run by the same user? → **A separate access-control axis assigning each process an integrity level (Low/Medium/High/System); a lower-integrity process generally cannot manipulate a higher-integrity one, even under the identical user account**
3. Why does the UAC prompt appear on the "Secure Desktop"? → **To prevent ordinary user-mode malware from spoofing the prompt or automating a click, since the Secure Desktop is isolated from normal desktop processes**
4. Is UAC considered a hard security boundary by Microsoft? → **No — it's officially a convenience/defense-in-depth feature, which is why UAC bypass techniques exist and are not treated as critical-severity vulnerabilities by Microsoft**
5. Does a UAC bypass let a completely unprivileged standard user become an administrator? → **No — it only elevates an already Administrators-group account from Medium to High integrity; it doesn't cross the standard-user-to-admin boundary**

## 9. Key Points

- **Access tokens** carry a process's security identity; **Admin Approval Mode** splits an admin's logon into a filtered standard token (default) and an elevated token (on demand).
- **Mandatory Integrity Control** (System > High > Medium > Low > Untrusted) is a second, orthogonal access-control axis alongside normal ACLs.
- The **Secure Desktop** prevents UAC prompt spoofing by isolating the prompt from normal user-mode processes.
- **UAC is not a hard security boundary** — UAC bypass techniques exist and are officially treated as low-severity, since they only elevate Medium→High within an already-privileged account.

---
*Related: [[Users_Groups_and_SID]], [[Windows_Processes_and_Services]], [[Windows_Security_Features]], [[Windows_Privilege_Escalation]]*
