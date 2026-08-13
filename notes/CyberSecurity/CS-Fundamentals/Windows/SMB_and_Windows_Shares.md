## 1. Introduction

**SMB (Server Message Block)** is Windows' native file-and-printer-sharing protocol, underlying network drives, administrative shares, and much of internal Windows-to-Windows communication. Its long history of vulnerabilities (EternalBlue/WannaCry among the most infamous) and its centrality to lateral movement make it one of the single most important protocols to understand in Windows-focused security work.

## 2. SMB Versions

| Version | Introduced with | Notes |
|---|---|---|
| SMBv1 | Windows NT / LAN Manager era | **Deprecated, insecure** — vulnerable to EternalBlue (MS17-010); should be disabled everywhere |
| SMBv2 | Windows Vista/Server 2008 | Major performance/security overhaul over v1 |
| SMBv3 | Windows 8/Server 2012 | Adds end-to-end encryption, improved performance (multichannel), pre-authentication integrity |

```powershell
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol      # remove SMBv1 entirely
Set-SmbServerConfiguration -EncryptData $true                             # enforce SMBv3 encryption
```

## 3. Shares — Types and Enumeration

| Share type | Notes |
|---|---|
| Standard share | Explicitly created, visible in browsing (`\\server\ShareName`) |
| **Administrative share** | Auto-created, hidden (name ends in `$`) — `C$`, `D$` (root of each drive), `ADMIN$` (Windows directory), `IPC$` (inter-process communication, no file access) |
| Hidden share | Any share ending in `$`, including custom ones — hidden from browsing, but still accessible if the exact path is known |

```cmd
net share                              :: list local shares
net share MyShare=C:\Data /grant:Everyone,FULL     :: create a share
net view \\Server01                                   :: enumerate a remote host's visible shares
```

```powershell
Get-SmbShare
New-SmbShare -Name "MyShare" -Path "C:\Data" -FullAccess "Everyone"
Get-SmbShareAccess -Name "MyShare"
```

**`IPC$` and null sessions:** `IPC$` exists purely to support inter-process communication (RPC over SMB) — historically, older Windows versions allowed anonymous, unauthenticated "**null sessions**" against `IPC$` to enumerate users, groups, and shares without any credentials at all. Modern Windows restricts this heavily by default, but null-session-style misconfigurations are still occasionally found, especially against older systems.

## 4. Authentication Over SMB

SMB authentication uses the same underlying mechanisms as [[Windows_Authentication]] — **NTLM** or **Kerberos**, depending on the environment (workgroup vs domain). This is exactly why SMB is central to credential-based attacks:

| Attack | Mechanism |
|---|---|
| SMB relay | Capturing an NTLM authentication attempt (e.g., via a poisoned LLMNR/NBT-NS response) and relaying it to a *different* target server, authenticating as the victim without knowing their password |
| Pass-the-Hash over SMB | Using a stolen NTLM hash directly to authenticate to SMB shares/services, without cracking it |
| SMB signing bypass | If SMB signing isn't enforced, relay attacks succeed even with a valid response; enforced signing largely mitigates relay |

## 5. EternalBlue — Why SMBv1 Matters Historically

**MS17-010 (EternalBlue)** was a critical remote code execution vulnerability in SMBv1, allegedly developed by the NSA and leaked publicly in 2017. It was weaponized almost immediately in **WannaCry** and **NotPetya**, two of the most damaging ransomware/wiper outbreaks in history — both spread automatically across networks purely by exploiting unpatched SMBv1, no user interaction required. This remains the canonical real-world case study for both "why disable legacy protocols" and "why patch management matters" (see [[Windows_Update_and_Patch_Management]]).

## 6. SMB Signing and Encryption

| Control | Purpose |
|---|---|
| SMB Signing | Cryptographically signs each SMB packet, preventing tampering and largely defeating relay attacks — should be **required**, not just enabled, on servers (especially Domain Controllers) |
| SMB Encryption (SMBv3+) | Encrypts data in transit end-to-end |

```powershell
Get-SmbServerConfiguration | Select-Object RequireSecuritySignature, EnableSecuritySignature
Set-SmbServerConfiguration -RequireSecuritySignature $true
```

```
Group Policy: Computer Configuration → Windows Settings → Security Settings → Local Policies → Security Options
  → "Microsoft network server: Digitally sign communications (always)"
```

## 7. Practical Enumeration and Access

```cmd
net use \\Server01\Share                           :: connect to a share (uses current credentials)
net use \\Server01\Share /user:DOMAIN\user password     :: connect with explicit credentials
dir \\Server01\C$                                            :: browse an administrative share (requires admin rights)
```

```powershell
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\Server01\Share"
Test-Path "\\Server01\C$"
```

Common third-party enumeration tooling (`smbclient`, `enum4linux`, `CrackMapExec`/`NetExec`) is run from Linux against Windows SMB targets constantly in penetration testing — worth knowing even though this note focuses on the Windows-native side.

## 8. Interview Questions

1. What made SMBv1 specifically dangerous, and what real-world incidents exploited it? → **The MS17-010 (EternalBlue) vulnerability allowed unauthenticated remote code execution; it was weaponized in WannaCry and NotPetya, both of which spread automatically across networks with no user interaction**
2. What are the Windows administrative shares, and why are they notable? → **Hidden, auto-created shares like `C$`, `ADMIN$`, and `IPC$` — accessible to administrators by default, and a common target for lateral movement (e.g., copying/executing files via `C$`/`ADMIN$`) once admin-equivalent credentials are obtained**
3. What is a null session, and why is it a security concern? → **An anonymous, unauthenticated connection to `IPC$` historically allowing enumeration of users/groups/shares without credentials; modern Windows restricts it by default, but misconfigurations still occur**
4. How does SMB signing mitigate relay attacks? → **It cryptographically signs each packet, so a captured/relayed authentication can't be used to tamper with or forge subsequent traffic — relay attacks largely fail when signing is required rather than merely enabled**
5. What's the practical difference between SMB relay and Pass-the-Hash? → **Relay forwards a captured live authentication attempt to a different target in real time; Pass-the-Hash uses an already-obtained NTLM hash directly to authenticate, independent of any live victim session**

## 9. Key Points

- **SMBv1 is deprecated and should be disabled** — it's the protocol behind EternalBlue/WannaCry/NotPetya; SMBv3 adds encryption and integrity protections SMBv1 lacks entirely.
- **Administrative shares** (`C$`, `ADMIN$`, `IPC$`) are hidden but present by default and are a standard lateral-movement pathway once admin credentials are compromised.
- SMB authentication rides on the same **NTLM/Kerberos** mechanisms covered in [[Windows_Authentication]], making SMB relay and Pass-the-Hash core SMB-specific attack techniques.
- **Requiring** (not just enabling) SMB signing is the primary mitigation against relay attacks.

---
*Related: [[Windows_Authentication]], [[Windows_Networking_Commands]], [[Windows_Update_and_Patch_Management]], [[Windows_Persistence_and_Lateral_Movement]]*
