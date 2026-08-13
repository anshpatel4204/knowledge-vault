## 1. Introduction

**Task Scheduler** is Windows' native mechanism for running programs or scripts automatically, based on a time schedule or a system event — used constantly for legitimate maintenance (disk cleanup, update checks, backups) and, just as often, abused by attackers as a **persistence** mechanism, since a scheduled task survives reboots and doesn't require a service to be installed.

## 2. Task Scheduler Structure

| Component | Description |
|---|---|
| Trigger | What causes the task to run — a time, a schedule, a system event, at logon/startup, or on idle |
| Action | What the task actually does — run a program, send an email (deprecated), display a message (deprecated) |
| Condition | Extra requirements — e.g., only run if on AC power, only if network available |
| Settings | Retry behavior, timeout, whether to run with highest privileges |
| Security context | The account the task runs as — can be a specific user, SYSTEM, or another service account |

## 3. GUI and Command-Line Management

```
taskschd.msc                :: GUI Task Scheduler
```

```cmd
schtasks /query /fo LIST /v                                        :: list all tasks, verbose
schtasks /query /tn "MyTask"                                           :: query a specific task
schtasks /create /tn "MyTask" /tr "C:\script.bat" /sc daily /st 09:00     :: create a daily task
schtasks /run /tn "MyTask"                                                   :: run immediately
schtasks /delete /tn "MyTask" /f                                                :: delete
schtasks /change /tn "MyTask" /disable                                            :: disable
```

```powershell
Get-ScheduledTask
Get-ScheduledTask | Where-Object State -eq "Ready"
Get-ScheduledTaskInfo -TaskName "MyTask"

$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\script.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At 9am
Register-ScheduledTask -TaskName "MyTask" -Action $action -Trigger $trigger -RunLevel Highest

Unregister-ScheduledTask -TaskName "MyTask" -Confirm:$false
```

## 4. Trigger Types

| Trigger | Fires when |
|---|---|
| Time-based | At a specific date/time, or on a recurring schedule (daily/weekly/monthly) |
| At logon | When any (or a specific) user logs on |
| At startup | When the system boots |
| On an event | A specific Windows Event Log entry occurs (see [[Event_Log_Management]]) |
| On idle | The system has been idle for a set duration |
| At task creation/modification | Immediately, for testing |

## 5. Where Task Definitions Live

Scheduled tasks are stored as XML files and registry entries — both worth knowing for forensic/hunting purposes:

| Location | Contents |
|---|---|
| `C:\Windows\System32\Tasks\` | XML definition files, one per task, in a folder structure mirroring Task Scheduler's UI folders |
| `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache` | Registry cache of task metadata |
| Event Log `Microsoft-Windows-TaskScheduler/Operational` | Logs task registration, execution, and results |

## 6. Security Relevance — Persistence and Privilege Considerations

| Aspect | Relevance |
|---|---|
| Persistence | A task with a Logon/Startup/recurring trigger survives reboot and re-executes the attacker's payload without any service installation |
| Elevated execution | Tasks can be configured to "Run with highest privileges" — if a low-privileged user can modify a task that runs as SYSTEM/Administrator, that's a direct privilege escalation |
| "Run whether user is logged on or not" | Stores credentials (or uses a saved token) so the task runs unattended, extending its stealth |
| Masquerading | Attackers often name malicious tasks to resemble legitimate Microsoft/vendor maintenance tasks to blend into the large default task list |

```powershell
# Hunt: recently created/modified tasks, and tasks with suspicious actions
Get-ScheduledTask | Where-Object { $_.Actions.Execute -match "powershell|cmd|wscript|mshta" } |
    Select-Object TaskName, State, @{N="Action";E={$_.Actions.Execute}}
```

## 7. Task Privileges vs the Running User

Similar to services (see [[Windows_Processes_and_Services]]), if the account a task runs as has broader rights than the user who's able to *modify* that task, an attacker who compromises the low-privileged account can edit the task to escalate — the same "weak permissions on a privileged automation object" pattern seen across services and scheduled tasks alike.

```cmd
icacls "C:\Windows\System32\Tasks\MyTask"      :: check who can modify the task's underlying file
```

## 8. Interview Questions

1. What are the four core components of a scheduled task? → **Trigger (when it runs), Action (what it does), Condition (extra requirements), and Security context (the account it runs as)**
2. Where are scheduled task definitions physically stored? → **As XML files under `C:\Windows\System32\Tasks\`, with a metadata cache in the registry under `TaskCache`**
3. Why is Task Scheduler a common persistence mechanism for malware? → **Tasks with logon/startup/recurring triggers survive reboot and re-execute automatically without requiring service installation, and can blend in among many legitimate default tasks**
4. What makes a scheduled task a privilege-escalation vector? → **If it's configured to run as SYSTEM/Administrator with "highest privileges," and a lower-privileged user has write access to modify the task, they can substitute their own payload to run at that elevated level**
5. What Event Log channel records scheduled task execution history? → **`Microsoft-Windows-TaskScheduler/Operational`**

## 9. Key Points

- Tasks combine a **Trigger**, an **Action**, optional **Conditions**, and a **security context** (the account they run as).
- `schtasks` (CLI) and `Get-ScheduledTask`/`Register-ScheduledTask` (PowerShell) are the primary management tools; `taskschd.msc` is the GUI.
- Task definitions live as **XML files** under `C:\Windows\System32\Tasks\`, a key forensic/hunting location.
- Scheduled tasks are a top-tier **persistence** mechanism, and — when misconfigured with weak edit permissions on a privileged task — a **privilege-escalation** vector too.

---
*Related: [[Windows_Processes_and_Services]], [[Event_Log_Management]], [[Windows_Persistence_and_Lateral_Movement]], [[Windows_Privilege_Escalation]]*
