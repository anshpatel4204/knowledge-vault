## 1. Introduction

Windows Event Logs are the OS's primary built-in audit trail — recording logons, process creation, service changes, policy changes, and application errors. For a defender, they are the single most important native data source for detection and incident response; for an attacker, they're something to evade or clear. Effective use of Event Viewer, the underlying EVTX format, and enhanced tooling like **Sysmon** is a core blue-team skill.

## 2. Log Categories

| Log | Contents |
|---|---|
| Application | App-level events/errors |
| System | OS/driver/service-level events |
| **Security** | Logon/logoff, object access, policy changes, privilege use — the most security-critical log |
| Setup | OS installation/update events |
| Forwarded Events | Events collected from other machines via Windows Event Forwarding (WEF) |

```
eventvwr.msc                :: GUI Event Viewer
```

```powershell
Get-WinEvent -LogName Security -MaxEvents 50
Get-EventLog -LogName Security -Newest 50            # older cmdlet, still common in scripts
Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624} -MaxEvents 20
```

```cmd
wevtutil qe Security /c:20 /f:text          :: query events from cmd
wevtutil el                                     :: list all available log channels
```

## 3. Key Security Event IDs

| Event ID | Meaning |
|---|---|
| 4624 | Successful logon |
| 4625 | **Failed** logon — key for brute-force detection |
| 4634 / 4647 | Logoff |
| 4648 | Logon using explicit credentials ("runas") — often indicates lateral movement |
| 4672 | Special privileges (admin-equivalent) assigned to a new logon |
| 4688 | New process created — includes command line if enabled, extremely high detection value |
| 4697 | A service was installed on the system |
| 4720 | A user account was created |
| 4732 / 4728 | A member was added to a local/global privileged group |
| 4104 | PowerShell Script Block Logging (see [[PowerShell_Scripting]]) |
| 1102 | The Security audit log was **cleared** — a major red flag, since attackers do this to cover tracks |

**Logon Type codes** (part of the 4624/4625 event data) tell you *how* the logon happened:

| Logon type | Meaning |
|---|---|
| 2 | Interactive (physically at the keyboard) |
| 3 | Network (e.g., accessing a share) |
| 4 | Batch (scheduled task) |
| 5 | Service |
| 10 | RemoteInteractive (RDP) |

## 4. Enabling Deeper Auditing

By default, Windows doesn't log everything relevant to security — administrators must enable specific **audit policies** (via Local Security Policy or Group Policy) and, for process command lines, a separate setting.

```
secpol.msc → Local Policies → Audit Policy         :: legacy audit policy
secpol.msc → Advanced Audit Policy Configuration       :: modern, granular audit policy
Group Policy → Administrative Templates → System → Audit Process Creation → Include command line
```

```powershell
auditpol /get /category:*                    # view current audit policy
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
```

## 5. Sysmon — Enhanced Telemetry

**Sysmon (System Monitor)**, a free Sysinternals tool, dramatically extends native logging with rich, configurable detail — process creation with full command lines and hashes, network connections, file creation time changes, registry modifications, and more — written to its own dedicated Event Log channel.

```
Sysmon64.exe -i sysmonconfig.xml         :: install with a config file
Sysmon64.exe -c                              :: show current configuration
```

| Sysmon Event ID | Meaning |
|---|---|
| 1 | Process creation (with hash, command line, parent process) |
| 3 | Network connection |
| 7 | Image (DLL) loaded |
| 11 | File created |
| 12/13/14 | Registry key/value created, modified, deleted |
| 22 | DNS query |

Sysmon logs appear under `Microsoft-Windows-Sysmon/Operational` and are the backbone of most SOC detection rules that go beyond native Windows logging.

## 6. Log Storage and EVTX Format

Event logs are stored on disk as `.evtx` (Event Log XML) files, a binary XML format:

```
C:\Windows\System32\winevt\Logs\Security.evtx
```

| Behavior | Notes |
|---|---|
| Log rotation | Older events overwrite newest by default once max size is reached — a common reason attackers don't always need to clear logs, just wait |
| Max size configuration | `wevtutil sl Security /ms:<bytes>` |
| Forensic collection | Investigators pull `.evtx` files directly for offline analysis (via tools like Event Log Explorer, `python-evtx`, or Timeline Explorer) |

## 7. Anti-Forensics — Log Clearing and Evasion

| Technique | Detection angle |
|---|---|
| Clearing the Security log (`wevtutil cl Security`) | Generates Event ID **1102** in the very act of clearing — a near-guaranteed alert if monitored |
| Disabling auditing before an action | Detectable via `auditpol` change events, or absence of expected events during a known active window |
| Log tampering at the file level | Rare/advanced; requires SYSTEM-level access and specialized tooling; centralized log forwarding (WEF/SIEM) defeats this since a copy already left the host |

**Defensive principle:** forwarding logs off-host in near-real-time (to a SIEM, or via Windows Event Forwarding) is the single strongest mitigation against local log tampering, since the attacker would need to compromise the collector too.

## 8. Interview Questions

1. Which Event ID indicates a failed logon, and why does it matter? → **4625 — critical for detecting brute-force/password-spray attacks**
2. What does Event ID 4688 capture, and what setting is needed to see full command lines in it? → **New process creation; command-line logging requires enabling "Include command line in process creation events" via Group Policy, since it's not on by default**
3. Why is Event ID 1102 so significant? → **It records that the Security audit log was cleared — a strong indicator an attacker is covering their tracks, since clearing the log itself generates this event**
4. What does Sysmon add over native Windows Event Logging? → **Much richer, configurable telemetry — process hashes/command lines, network connections, DLL loads, registry changes, file creation — written to its own dedicated log channel**
5. What's the strongest defense against an attacker clearing or tampering with local event logs? → **Forwarding logs off-host in near-real-time (SIEM ingestion / Windows Event Forwarding), so a copy exists independently of what the attacker can touch locally**

## 9. Key Points

- The **Security** log (logons, process creation, privilege use, policy changes) is the highest-value native log for detection.
- Key event IDs to know cold: **4624/4625** (logon success/fail), **4688** (process creation), **4720/4732** (account/group changes), **4104** (PowerShell logging), **1102** (log cleared).
- Deeper auditing (especially command-line logging on 4688) must be **explicitly enabled** — it isn't on by default.
- **Sysmon** massively extends native telemetry and underpins most real-world SOC detection engineering.
- **Off-host log forwarding** is the strongest defense against local log tampering/clearing.

---
*Related: [[PowerShell_Scripting]], [[Task_Scheduler]], [[Windows_Security_Features]], [[Windows_Persistence_and_Lateral_Movement]]*
