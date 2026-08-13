## 1. Introduction

Windows manages running work through **processes**, **threads**, and a special category of long-running background processes called **services** — each with distinct startup, monitoring, and security characteristics. Understanding this layer is critical for both routine administration and identifying malicious activity, since a huge share of Windows persistence and defense-evasion techniques revolve around processes and services.

## 2. Processes and Threads

| Term | Meaning |
|---|---|
| Process | An instance of a running program — has its own memory space, handle table, and security token |
| Thread | A unit of execution within a process; a process has at least one, often many |
| PID | Process ID — unique identifier for a running process |
| PPID | Parent Process ID — the process that created it |

```cmd
tasklist                             :: list running processes
tasklist /v                             :: verbose, includes window title, session, memory
taskkill /PID 1234 /F                      :: force-kill a process by PID
taskkill /IM notepad.exe /F                   :: kill by image name
```

```powershell
Get-Process
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Stop-Process -Name notepad -Force
Get-Process -Id 1234 | Select-Object Id, ProcessName, Path, Parent
```

## 3. Viewing Parent-Child Relationships

Understanding what spawned what is central to incident response — a `winword.exe` process spawning `cmd.exe` or `powershell.exe`, for instance, is a classic macro-malware execution pattern.

```powershell
Get-CimInstance Win32_Process | Select-Object ProcessId, ParentProcessId, Name, CommandLine
```

**Process Explorer** (Sysinternals) and **Process Hacker** are the standard GUI tools for visualizing this tree interactively, alongside command lines and loaded DLLs — far more detail than Task Manager exposes by default.

## 4. Services — The Service Control Manager

A **Windows Service** is a special process type designed to run in the background, independent of any logged-in user, typically starting automatically at boot. Services are managed by the **Service Control Manager (SCM)**, running as `services.exe` (see [[Windows_Boot_Process]]).

```cmd
services.msc                                     :: GUI service manager
sc query wuauserv                                   :: query a service's status
sc query state= all                                    :: list all services
sc start wuauserv                                         :: start a service
sc stop wuauserv                                             :: stop a service
sc config wuauserv start= demand                                :: change startup type
sc create MyService binPath= "C:\path\svc.exe"                     :: create a new service
```

```powershell
Get-Service
Get-Service | Where-Object Status -eq "Running"
Start-Service -Name wuauserv
Stop-Service -Name wuauserv
Set-Service -Name wuauserv -StartupType Automatic
New-Service -Name "MyService" -BinaryPathName "C:\path\svc.exe"
```

## 5. Service Startup Types

| Startup type | Behavior |
|---|---|
| Automatic | Starts at boot, before user logon |
| Automatic (Delayed Start) | Starts shortly after boot, reducing startup congestion |
| Manual | Starts only when explicitly triggered (by another service, an app, or an admin) |
| Disabled | Cannot be started at all until re-enabled |

## 6. svchost.exe — The Shared Service Host

Many Windows services are actually DLLs, not standalone executables, and run hosted inside shared **`svchost.exe`** processes grouped by service type — which is exactly why Task Manager often shows dozens of `svchost.exe` entries.

```cmd
tasklist /svc                                              :: show which services run under which svchost PID
```

```powershell
Get-CimInstance Win32_Service | Where-Object { $_.PathName -match "svchost" } | Select-Object Name, ProcessId
```

**Security relevance:** because legitimate `svchost.exe` is so common and expected, it's a frequent malware masquerading target — attackers name malicious binaries `svchost.exe` and place them outside `C:\Windows\System32` (the only legitimate location), which is a key detection heuristic.

## 7. Service Security — Permissions and Misconfiguration

Services run under a configured account (LocalSystem, LocalService, NetworkService, or a specific user) and reference a binary path. Two classic misconfigurations are common privilege-escalation vectors, covered fully in [[Windows_Privilege_Escalation]]:

| Misconfiguration | Risk |
|---|---|
| Unquoted service path with spaces (e.g., `C:\Program Files\My App\svc.exe`) | Windows tries each space-delimited segment as a potential executable — an attacker who can write to `C:\Program.exe` hijacks execution |
| Weak service binary/folder permissions | If a low-privileged user can overwrite the service's binary, and the service runs as SYSTEM, that's a direct SYSTEM-level privilege escalation |
| Weak service registry permissions | Modifying `ImagePath` under the service's registry key achieves the same result without touching the file at all |

```cmd
accesschk.exe -uwcqv "Authenticated Users" *          :: (Sysinternals) find services writable by low-privileged users
```

## 8. Interview Questions

1. What's the relationship between svchost.exe and Windows services? → **Many services are implemented as DLLs rather than standalone EXEs, and run hosted inside shared `svchost.exe` processes grouped by service type**
2. Why is an unquoted service path with spaces a security risk? → **Windows tries each space-delimited segment in order as a potential executable path, so an attacker who can place a file at an earlier segment (e.g., `C:\Program.exe`) can hijack execution when the service starts**
3. What's the difference between Automatic and Automatic (Delayed Start)? → **Automatic starts immediately at boot before logon; Delayed Start starts shortly after boot to reduce startup resource contention**
4. What component manages Windows services, and what process implements it? → **The Service Control Manager (SCM), implemented as `services.exe`**
5. Why is `svchost.exe` a common malware masquerading target? → **It's a legitimate, expected, and very common process name, so malware disguised as it can blend in — the key detection heuristic is checking it's actually running from `C:\Windows\System32`**

## 9. Key Points

- A **process** owns memory/handles/a security token; a **thread** is a unit of execution within it; **services** are background processes managed by the **SCM (`services.exe`)**.
- `tasklist`/`Get-Process` and `sc`/`Get-Service`/`services.msc` are the core native tools for each.
- **`svchost.exe`** hosts many DLL-based services grouped by type — and is consequently a common malware disguise.
- **Unquoted service paths** and **weak service binary/registry permissions** are classic, still-common Windows privilege-escalation vectors.
- Parent-child process relationships (`Win32_Process.ParentProcessId`) are a core incident-response signal for spotting malicious execution chains.

---
*Related: [[Windows_Architecture]], [[Windows_Registry]], [[UAC_and_Privilege_Levels]], [[Windows_Privilege_Escalation]]*
