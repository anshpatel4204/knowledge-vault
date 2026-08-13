## 1. Introduction

Modern Windows ships with a substantial built-in security stack — beyond just antivirus, it includes application control, disk encryption, network filtering, and hardware-backed credential isolation. Understanding what's actually built in (and its real limits) matters for both configuring a system defensibly and for realistically assessing what an attacker still has to work around.

## 2. Windows Defender / Microsoft Defender Antivirus

Built-in, real-time antimalware, enabled by default when no third-party AV is installed.

| Component | Role |
|---|---|
| Real-time protection | Scans files/processes on access |
| Cloud-delivered protection | Sends suspicious sample metadata to Microsoft for near-real-time verdicts |
| Behavior monitoring | Flags suspicious behavior patterns, not just known signatures |
| **Defender for Endpoint** | The enterprise EDR tier — telemetry, hunting, automated response, integrates with a SOC |

```powershell
Get-MpComputerStatus
Get-MpThreatDetection
Start-MpScan -ScanType QuickScan
Set-MpPreference -DisableRealtimeMonitoring $false
```

## 3. Windows Firewall

A host-based, stateful firewall, profile-aware (Domain/Private/Public), managed via GUI, `netsh`, or PowerShell.

```powershell
Get-NetFirewallProfile
Get-NetFirewallRule | Where-Object Enabled -eq True
New-NetFirewallRule -DisplayName "Block Telnet" -Direction Outbound -Protocol TCP -RemotePort 23 -Action Block
```

```cmd
netsh advfirewall show allprofiles
netsh advfirewall set allprofiles state on
```

## 4. BitLocker — Disk Encryption

**BitLocker** provides full-volume encryption, typically backed by a **TPM (Trusted Platform Module)** that seals the encryption key to the specific machine's known-good boot state — if boot components are tampered with, the TPM refuses to release the key.

```powershell
Get-BitLockerVolume
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -TpmProtector
Suspend-BitLocker -MountPoint "C:"           # temporarily disable (e.g., during a BIOS update)
manage-bde -status                                :: cmd equivalent status check
```

**Security relevance:** BitLocker protects data confidentiality **at rest** — it does nothing once Windows has booted and the volume is mounted/decrypted for a logged-in session; it's specifically a defense against offline attacks (stolen laptop, disk removed and read from another machine).

## 5. AppLocker and WDAC — Application Control

Both restrict which applications are allowed to run, moving beyond antivirus's "block known-bad" model to a "only allow known-good" (allowlisting) model:

| Feature | Description |
|---|---|
| **AppLocker** | Rule-based (path, publisher, hash) allow/deny for EXE, script, MSI, packaged app execution — Enterprise/Education editions |
| **WDAC (Windows Defender Application Control)** | Kernel-enforced, more robust than AppLocker (can't be bypassed the same ways); the modern recommended approach, available more broadly |

```powershell
Get-AppLockerPolicy -Effective
New-AppLockerPolicy -FileInformation $fileInfo -RuleType Publisher -User Everyone
```

Application allowlisting is one of the most effective practical controls against unknown/novel malware, since it doesn't rely on recognizing the malicious file at all — only on recognizing that it *isn't* on the approved list.

## 6. Credential Guard

Uses **Virtualization-Based Security (VBS)** — a hardware-virtualized, isolated memory region — to protect LSASS secrets (NTLM hashes, Kerberos tickets) from being read even by an attacker with SYSTEM-level access, directly countering the credential-dumping techniques covered in [[Windows_Authentication]].

```powershell
Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard
```

## 7. Windows Hello / Windows Hello for Business

Replaces password-based authentication with biometrics (fingerprint/face) or a PIN, backed by a hardware TPM — critically, the PIN/biometric only unlocks a *locally-stored* cryptographic key, meaning there's no network-transmittable password-equivalent for an attacker to phish or intercept in the same way as a traditional password.

## 8. Exploit Protection and Attack Surface Reduction (ASR)

| Feature | Purpose |
|---|---|
| Exploit Protection | Mitigations like DEP, ASLR, Control Flow Guard applied per-process (successor to the legacy EMET tool) |
| Attack Surface Reduction (ASR) rules | Granular rules blocking specific risky behaviors (e.g., "block Office apps from creating child processes," "block execution of potentially obfuscated scripts") — highly effective against common macro-malware and living-off-the-land techniques |

```powershell
Get-MpPreference | Select-Object -ExpandProperty AttackSurfaceReductionRules_Ids
Set-MpPreference -AttackSurfaceReductionRules_Ids <RuleGUID> -AttackSurfaceReductionRules_Actions Enabled
```

## 9. Summary Table — Feature vs What It Actually Defends Against

| Feature | Primary defense |
|---|---|
| Defender AV/EDR | Known/behaviorally-suspicious malware detection and response |
| Firewall | Unwanted inbound/outbound network connections |
| BitLocker | Data confidentiality when a disk is offline/stolen |
| AppLocker/WDAC | Execution of unapproved/unknown binaries |
| Credential Guard | In-memory credential theft even with SYSTEM access |
| Windows Hello | Password-equivalent phishing/interception |
| ASR rules | Specific common attack techniques (macro spawning shells, script obfuscation) |

## 10. Interview Questions

1. What does BitLocker actually protect against, and what does it *not* protect against? → **It protects data confidentiality when a disk is offline (stolen device, removed disk); it provides no protection once the volume is mounted and Windows is running/logged in**
2. What's the key difference between AppLocker and WDAC? → **WDAC is kernel-enforced and more robust against bypass techniques; AppLocker is more flexible/user-friendly but relies on components that determined attackers can more easily circumvent**
3. How does Credential Guard protect LSASS secrets even from a SYSTEM-level attacker? → **It uses Virtualization-Based Security to isolate secrets in a separate, hardware-virtualized memory region that even SYSTEM-privileged code on the host OS cannot directly read**
4. Why is application allowlisting (AppLocker/WDAC) considered more effective against unknown malware than traditional antivirus? → **It doesn't need to recognize a file as malicious — it only needs to recognize that the file isn't on the approved list, which also blocks entirely novel/unseen malware**
5. What category of Windows Defender feature specifically targets techniques like "Office spawning PowerShell" or obfuscated scripts? → **Attack Surface Reduction (ASR) rules**

## 11. Key Points

- Windows' built-in security stack spans **Defender (AV/EDR), Firewall, BitLocker, AppLocker/WDAC, Credential Guard, Windows Hello, and ASR rules** — each defends a distinct threat category, not a single unified thing.
- **BitLocker protects data at rest only** — it's irrelevant once a system is booted and logged in.
- **AppLocker/WDAC allowlisting** is more resilient against unknown malware than traditional signature-based AV, with **WDAC** being the stronger, kernel-enforced option.
- **Credential Guard** directly counters LSASS credential-dumping techniques via hardware-backed VBS isolation.

---
*Related: [[Windows_Authentication]], [[UAC_and_Privilege_Levels]], [[Windows_Privilege_Escalation]], [[Windows_Persistence_and_Lateral_Movement]]*
