## 1. Introduction

This capstone note ties together the folder's earlier topics — the registry, services, scheduled tasks, WMI, and authentication — into how attackers actually **stay** inside a compromised Windows environment (**persistence**) and **spread** to other machines (**lateral movement**). Recognizing these patterns is essential for incident response and detection engineering.

## 2. Persistence — Registry-Based

Already introduced in [[Windows_Registry]]:

| Location | Mechanism |
|---|---|
| `HKLM/HKCU ...\CurrentVersion\Run` / `RunOnce` | Executes a program at every (or next) logon |
| `Winlogon\Shell` / `Userinit` | Hijacks or appends to what runs at logon, alongside `explorer.exe` |
| Image File Execution Options (IFEO) Debugger key | Redirects execution of a targeted legitimate binary to a malicious one whenever it's launched |
| `AppInit_DLLs` | Forces a DLL to load into every process using `user32.dll` (largely mitigated on modern Windows with Secure Boot, but still checked) |

## 3. Persistence — Services and Scheduled Tasks

Already introduced in [[Windows_Processes_and_Services]] and [[Task_Scheduler]]:

| Mechanism | Notes |
|---|---|
| Malicious service (Auto-start) | Survives reboot, runs as SYSTEM by default unless configured otherwise |
| Scheduled task (Logon/Startup/recurring trigger) | Same effect, often used to blend in with the large default task list |
| Service/task masquerading | Naming the artifact to resemble a legitimate Microsoft/vendor process |

## 4. Persistence — WMI Event Subscriptions

Already introduced in [[WMI_and_CIM]] — `__EventFilter` + `__EventConsumer` + `__FilterToConsumerBinding` triggers a payload on a condition (e.g., process start, timer interval, system startup), entirely within the WMI repository rather than a normal file/registry location, making it notably **fileless** and easy to overlook.

## 5. Persistence — Other Common Techniques

| Technique | Mechanism |
|---|---|
| Startup folder | Dropping a shortcut/executable in `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup` (per-user) or its all-users equivalent |
| DLL hijacking / search order abuse | Placing a malicious DLL where a legitimate application will load it due to Windows' DLL search order, achieving persistence *and* privilege inheritance from that application |
| Golden Ticket (domain-wide) | A forged Kerberos TGT from a stolen krbtgt hash, granting durable, domain-wide access until krbtgt is rotated twice (see [[Windows_Authentication]]) |
| New/backdoor domain account | Creating a new AD account, or adding an existing one to a privileged group, as a durable access method independent of any single exploited machine |
| Directory Services Restore Mode (DSRM) password abuse | The DSRM local admin account on Domain Controllers is rarely rotated and provides an alternate, often-overlooked authentication path |

## 6. Lateral Movement — Credential-Based

Directly built on [[Windows_Authentication]] and [[SMB_and_Windows_Shares]]:

| Technique | Mechanism |
|---|---|
| Pass-the-Hash | Authenticate using a stolen NTLM hash directly, without cracking the plaintext |
| Pass-the-Ticket | Reuse a stolen Kerberos ticket (TGT or service ticket) from another session |
| Overpass-the-Hash | Use an NTLM hash to request a legitimate Kerberos TGT, converting an NTLM-only compromise into full Kerberos access |
| SMB relay | Relay a captured NTLM authentication attempt to a different target in real time |
| Reused local admin passwords | Pivoting machine-to-machine using an identical local Administrator password across a fleet (mitigated by LAPS — see [[Domain_vs_Workgroup]]) |

## 7. Lateral Movement — Execution Methods

Once valid credentials/tickets are available for a target machine, several native Windows mechanisms let an attacker actually execute code remotely:

| Method | Underlying protocol |
|---|---|
| PsExec (Sysinternals) / `sc` remote service creation | Creates and runs a temporary Windows service on the target over SMB |
| WMI (`Invoke-CimMethod ... Win32_Process Create`) | Remote process creation via WMI (see [[WMI_and_CIM]]) |
| PowerShell Remoting (`Invoke-Command`) | WinRM-based remote script execution (see [[PowerShell_Scripting]]) |
| Scheduled task creation on a remote host | `schtasks /create /s <target>` — remote persistence + execution combined |
| RDP | Full interactive remote desktop access, using valid credentials |

These are, notably, all **legitimate administrative mechanisms** — this is the essence of "**living off the land**": using built-in Windows tools rather than custom malware, specifically to blend in with normal administrative activity and evade tools that only flag unknown binaries.

## 8. Detecting These Techniques — Tying Back to Earlier Notes

| Signal | Where to look |
|---|---|
| New/modified Run keys, services, scheduled tasks | Registry monitoring, Sysmon Event ID 13 (registry value set), Event ID 4697 (service installed) |
| Unusual logon types (Type 3/network, Type 10/RDP) from unexpected sources | Event ID 4624/4625, see [[Event_Log_Management]] |
| Explicit credential logons (Type 9) or "runas" activity | Event ID 4648 |
| WMI persistence | Query `root\subscription` for `__EventFilter`/`__EventConsumer` objects |
| PsExec-style remote service creation | Event ID 7045 (a new service was installed) combined with a short-lived, unusually-named service |
| Abnormal parent-child process chains | Sysmon Event ID 1 — e.g., `services.exe` spawning an unexpected binary, or `wmiprvse.exe` spawning `cmd.exe` |

## 9. Interview Questions

1. Why are WMI event subscriptions considered a particularly stealthy persistence technique? → **They live entirely within the WMI repository rather than a normal file or common registry location, making them fileless and easy for basic persistence-hunting tools to miss**
2. What's the difference between Pass-the-Hash and Overpass-the-Hash? → **Pass-the-Hash authenticates directly with a stolen NTLM hash; Overpass-the-Hash uses that NTLM hash to request a legitimate Kerberos TGT, upgrading NTLM-level access into full Kerberos ticket-based access**
3. Why is "living off the land" (using PsExec, WMI, PowerShell Remoting, scheduled tasks) attractive to attackers over custom malware? → **These are legitimate, expected administrative tools/protocols, so their use blends into normal activity and evades detections that only flag unknown/unsigned binaries**
4. What event ID would you look for to detect PsExec-style remote service-based execution? → **Event ID 7045 (a new service was installed), especially correlated with a short-lived or unusually-named service shortly followed by its removal**
5. Why is the DSRM (Directory Services Restore Mode) password on a Domain Controller a notable persistence concern? → **It's a local admin account rarely rotated by administrators, providing an alternate, often-overlooked authentication path on Domain Controllers**

## 10. Key Points

- Persistence mechanisms span **registry Run keys, services, scheduled tasks, WMI event subscriptions, DLL hijacking, and domain-level techniques** (Golden Tickets, backdoor accounts) — each covered individually in earlier notes in this folder.
- Lateral movement combines **credential/ticket theft** (Pass-the-Hash, Pass-the-Ticket, SMB relay) with **legitimate execution mechanisms** (PsExec, WMI, PowerShell Remoting, RDP) — the "living off the land" pattern.
- Detection ties directly back to the [[Event_Log_Management]] note: **Event IDs 4624/4625/4648/4697/7045**, plus Sysmon process-chain and registry telemetry, are the practical hunting signals for everything in this note.
- This note is deliberately the last in the roadmap — every technique here builds on the registry, services, WMI, authentication, and networking concepts covered throughout the rest of the Windows folder.

---
*Related: [[Windows_Registry]], [[Windows_Processes_and_Services]], [[WMI_and_CIM]], [[Windows_Authentication]], [[Event_Log_Management]], [[Windows_Privilege_Escalation]]*
