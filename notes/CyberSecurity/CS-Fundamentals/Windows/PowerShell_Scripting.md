## 1. Introduction

Beyond interactive one-liners (see [[PowerShell_Basics]]), PowerShell is a full scripting language with functions, modules, remoting, and a security-focused execution policy system — making it capable of everything from routine automation to full enterprise configuration management (DSC) and remote administration across an entire domain.

## 2. Script Basics

```powershell
# script.ps1
param(
    [string]$Name = "World",
    [int]$Count = 1
)

for ($i = 0; $i -lt $Count; $i++) {
    Write-Output "Hello, $Name! (iteration $i)"
}
```

```powershell
.\script.ps1 -Name "Ansh" -Count 3       # run with named parameters
```

## 3. Control Flow

```powershell
if ($x -gt 10) {
    Write-Output "big"
} elseif ($x -eq 10) {
    Write-Output "equal"
} else {
    Write-Output "small"
}

foreach ($item in $list) { Write-Output $item }

while ($x -lt 10) { $x++ }

switch ($value) {
    1 { "one" }
    2 { "two" }
    default { "other" }
}
```

| Comparison operator | Meaning |
|---|---|
| `-eq` / `-ne` | Equal / not equal |
| `-gt` / `-lt` | Greater than / less than |
| `-like` | Wildcard string match |
| `-match` | Regex match |
| `-contains` | Collection contains a value |

## 4. Functions

```powershell
function Get-Square {
    param([int]$Number)
    return $Number * $Number
}

Get-Square -Number 5     # 25
```

Advanced functions can behave like real cmdlets, with `[CmdletBinding()]` and full parameter validation:

```powershell
function Get-SafeDivision {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)][int]$Numerator,
        [Parameter(Mandatory=$true)][ValidateScript({$_ -ne 0})][int]$Denominator
    )
    return $Numerator / $Denominator
}
```

## 5. Modules

Modules package reusable functions/cmdlets for distribution:

```powershell
Get-Module -ListAvailable          # see installed modules
Import-Module ActiveDirectory          # load a module (e.g., AD cmdlets)
Install-Module -Name Az                   # install from PowerShell Gallery
Get-Command -Module ActiveDirectory          # list cmdlets a module provides
```

Common security-relevant modules: `ActiveDirectory` (AD administration), `PSReadLine` (shell UX), and offensive tooling like `PowerView`/`PowerSploit` (AD reconnaissance — well known in both red-team and defensive-detection contexts).

## 6. Execution Policy

PowerShell's **Execution Policy** is a *safety feature, not a security boundary* — it's trivially bypassed by anyone with local script access, but it does prevent accidental execution of untrusted scripts (e.g., double-clicking a malicious `.ps1` attachment).

| Policy | Behavior |
|---|---|
| Restricted | No scripts run at all (default on client Windows) |
| AllUsigned | All scripts must be digitally signed |
| RemoteSigned | Local scripts run freely; downloaded scripts need a signature |
| Unrestricted | All scripts run, with a warning for downloaded ones |
| Bypass | No restrictions, no warnings |

```powershell
Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
powershell -ExecutionPolicy Bypass -File script.ps1     # common bypass, often flagged by EDR
```

**Important:** because Execution Policy is not a real security boundary, it should never be relied upon as a control — actual security relies on **AppLocker/WDAC**, **Constrained Language Mode**, and **script signing** (see [[Windows_Security_Features]]).

## 7. PowerShell Remoting

Built on **WinRM (Windows Remote Management)**, PowerShell Remoting allows executing commands on remote machines — the PowerShell equivalent of SSH, and a common lateral-movement vector when credentials are compromised.

```powershell
Enable-PSRemoting -Force                                        # enable on the target (admin required)
Enter-PSSession -ComputerName Server01                              # interactive remote session
Invoke-Command -ComputerName Server01,Server02 -ScriptBlock { Get-Process }   # run on multiple machines
$session = New-PSSession -ComputerName Server01                        # persistent session object
```

## 8. Error Handling

```powershell
try {
    Get-Item "C:\DoesNotExist" -ErrorAction Stop
} catch {
    Write-Output "Error: $($_.Exception.Message)"
} finally {
    Write-Output "Cleanup runs regardless"
}
```

## 9. Security Relevance — Logging and Constrained Language Mode

| Control | Purpose |
|---|---|
| Script Block Logging | Logs the full content of executed script blocks, even if obfuscated — Event ID 4104 (see [[Event_Log_Management]]) |
| Module Logging | Logs pipeline execution details for specified modules |
| Transcription | Records an entire session's input/output to a text file |
| Constrained Language Mode | Restricts PowerShell to a safe subset — blocks direct .NET/COM access, common in post-exploitation tooling |
| PowerShell v2 downgrade attacks | Attackers deliberately invoke the legacy PowerShell v2 engine (if still installed) to bypass v5+ logging/AMSI — a well-known evasion technique, mitigated by removing the v2 engine entirely |

## 10. Interview Questions

1. Is PowerShell's Execution Policy a real security boundary? → **No — it's a safety feature to prevent accidental execution, trivially bypassed with `-ExecutionPolicy Bypass`; real enforcement requires AppLocker/WDAC and Constrained Language Mode**
2. What underlying protocol powers PowerShell Remoting? → **WinRM (Windows Remote Management)**
3. What's the difference between `Invoke-Command` and `Enter-PSSession`? → **`Invoke-Command` runs a script block non-interactively (optionally across many machines); `Enter-PSSession` opens a single interactive remote session**
4. What event ID is associated with PowerShell Script Block Logging? → **Event ID 4104**
5. Why do attackers sometimes deliberately invoke PowerShell v2? → **The legacy v2 engine lacks AMSI integration and modern logging present in v5+, letting malicious scripts evade detection — mitigated by uninstalling the v2 engine**

## 11. Key Points

- PowerShell scripts support **functions, modules, full control flow, and structured error handling** (`try/catch/finally`).
- **Execution Policy is not a security control** — it prevents accidents, not determined attackers; AppLocker/WDAC and Constrained Language Mode are the real enforcement layers.
- **PowerShell Remoting (WinRM)** is the built-in mechanism for both legitimate remote administration and lateral movement.
- **Script Block Logging (Event ID 4104)** is the single most valuable PowerShell-specific defensive telemetry source.
- PowerShell v2 downgrade attacks are a classic AMSI/logging-evasion technique — removing the legacy engine is the fix.

---
*Related: [[PowerShell_Basics]], [[WMI_and_CIM]], [[Event_Log_Management]], [[Windows_Security_Features]]*
