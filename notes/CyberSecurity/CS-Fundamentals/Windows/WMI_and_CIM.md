## 1. Introduction

**WMI (Windows Management Instrumentation)** is Windows' implementation of the **Common Information Model (CIM)**, an industry standard for representing and querying manageable resources (hardware, OS state, installed software, processes) through a unified, object-oriented model. It underlies a huge portion of Windows administration tooling — and, because of its power and remote-access capability, is also a major post-exploitation and lateral-movement mechanism.

## 2. Core Concepts

| Concept | Meaning |
|---|---|
| CIM | The abstract, cross-platform standard/data model (from the DMTF) |
| WMI | Microsoft's Windows-specific implementation of CIM |
| Namespace | A logical grouping of related classes, e.g., `root\cimv2` (most common) |
| Class | A schema describing a manageable resource, e.g., `Win32_Process`, `Win32_Service` |
| Instance | A specific object of a class — one running process, one installed service |
| WQL | WMI Query Language — SQL-like syntax for querying WMI classes |

## 3. Querying WMI — Command Line

```cmd
wmic process list brief                          :: list processes (legacy tool, deprecated but still present)
wmic service where "name='wuauserv'" get state       :: query a specific service
wmic os get caption,version,buildnumber                :: OS version info
wmic useraccount get name,sid                             :: local user accounts and SIDs
```

**Note:** `wmic.exe` is deprecated as of recent Windows releases in favor of PowerShell's CIM cmdlets, though it remains present and heavily used in both legitimate scripts and attacker tooling for now.

## 4. Querying WMI — PowerShell (Modern Approach)

```powershell
Get-CimInstance -ClassName Win32_Process                             # list processes
Get-CimInstance -ClassName Win32_Service | Where-Object State -eq "Running"
Get-CimInstance -ClassName Win32_OperatingSystem | Select-Object Caption, Version
Get-CimInstance -ClassName Win32_UserAccount

# WQL query directly
Get-CimInstance -Query "SELECT * FROM Win32_Process WHERE Name='notepad.exe'"

# The older, legacy cmdlet family (still seen in scripts)
Get-WmiObject -Class Win32_BIOS
```

`Get-CimInstance` (built on WSMan/WinRM) is the modern, recommended replacement for `Get-WmiObject` (built on the older DCOM-based WMI protocol) — both query the same underlying WMI repository but use different transport protocols.

## 5. Common WMI Classes

| Class | Represents |
|---|---|
| `Win32_Process` | Running processes |
| `Win32_Service` | Installed services |
| `Win32_OperatingSystem` | OS-level details |
| `Win32_ComputerSystem` | Machine/domain info |
| `Win32_UserAccount` | Local/domain user accounts |
| `Win32_LogicalDisk` | Disk volumes |
| `Win32_NetworkAdapterConfiguration` | Network interface config |
| `Win32_StartupCommand` | Programs configured to run at startup — a persistence-hunting favorite |

## 6. Remote WMI and Administration

WMI queries and method calls can target remote machines, making it a powerful (and dual-use) remote administration mechanism:

```powershell
Get-CimInstance -ClassName Win32_Process -ComputerName Server01 -Credential (Get-Credential)

# Remotely start a process on another machine (classic lateral movement technique)
Invoke-CimMethod -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine="calc.exe"} -ComputerName Server01
```

| Transport | Used by |
|---|---|
| DCOM | Legacy `Get-WmiObject`, `wmic.exe` — port 135 + dynamic RPC ports |
| WSMan (WinRM) | Modern `Get-CimInstance`, `Invoke-CimMethod` — port 5985 (HTTP) / 5986 (HTTPS) |

## 7. WMI Event Subscriptions — A Notable Persistence Technique

WMI supports **permanent event subscriptions** — filters that trigger an action whenever a specified condition occurs (e.g., "run this script every time a process named X starts," or "on every system startup"). Because these subscriptions live inside the WMI repository itself rather than a normal file or obvious registry Run key, they are a well-known **fileless persistence** technique used by advanced malware.

| Component | Role |
|---|---|
| `__EventFilter` | Defines the triggering condition (a WQL query, e.g., process creation, timer interval) |
| `__EventConsumer` | Defines the action to take (e.g., `CommandLineEventConsumer` runs a command) |
| `__FilterToConsumerBinding` | Links the filter to the consumer |

```powershell
Get-CimInstance -Namespace root\subscription -ClassName __EventFilter
Get-CimInstance -Namespace root\subscription -ClassName __EventConsumer
```

Hunting these two classes under `root\subscription` is a standard step in incident response threat hunting on Windows.

## 8. Practical Examples

```powershell
# Find all processes not running from C:\Windows or C:\Program Files (suspicious locations)
Get-CimInstance Win32_Process | Where-Object { $_.ExecutablePath -and $_.ExecutablePath -notmatch "^C:\\(Windows|Program Files)" }

# List all auto-start programs across common locations, including WMI-based persistence
Get-CimInstance Win32_StartupCommand | Select-Object Name, Command, Location

# Restart a service remotely
Invoke-CimMethod -ClassName Win32_Service -MethodName StopService -Filter "Name='Spooler'" -ComputerName Server01
```

## 9. Interview Questions

1. What's the relationship between CIM and WMI? → **CIM is the abstract cross-platform data model; WMI is Microsoft's Windows-specific implementation of it**
2. What's the modern cmdlet replacing `Get-WmiObject`, and what transport does it use? → **`Get-CimInstance`, using WSMan/WinRM instead of the legacy DCOM protocol**
3. What three WMI classes together implement a permanent event subscription? → **`__EventFilter`, `__EventConsumer`, and `__FilterToConsumerBinding`**
4. Why are WMI event subscriptions attractive as a persistence mechanism? → **They live inside the WMI repository rather than a normal file or common registry location, making them fileless and less likely to be checked by basic persistence-hunting tools**
5. What ports does DCOM-based WMI typically use vs WSMan-based CIM? → **DCOM: port 135 plus dynamic high RPC ports; WSMan: 5985 (HTTP) / 5986 (HTTPS)**

## 10. Key Points

- **WMI/CIM** exposes hardware, OS, and application state as queryable objects/classes (`Win32_Process`, `Win32_Service`, etc.) via **WQL**.
- `Get-CimInstance`/`Invoke-CimMethod` (WSMan-based) are the modern replacement for the legacy `Get-WmiObject`/`wmic.exe` (DCOM-based) tooling.
- WMI supports **remote querying and remote method invocation**, making it a dual-use administration and lateral-movement tool.
- **Permanent WMI event subscriptions** (`__EventFilter`/`__EventConsumer`/`__FilterToConsumerBinding`) are a well-known fileless persistence technique — covered further in [[Windows_Persistence_and_Lateral_Movement]].

---
*Related: [[PowerShell_Scripting]], [[Windows_Processes_and_Services]], [[Windows_Persistence_and_Lateral_Movement]]*
