## 1. Introduction

**PowerShell** is Microsoft's modern shell and scripting language, built on .NET. Unlike [[Command_Prompt_Basics]]'s plain-text pipeline, PowerShell passes structured **.NET objects** between commands — a fundamentally different (and far more powerful) model. It's the standard tool for real Windows administration today, and — because of that same power — also the dominant post-exploitation and living-off-the-land tool in modern Windows attacks.

## 2. Cmdlets — Verb-Noun Naming

PowerShell commands are called **cmdlets**, consistently named `Verb-Noun`:

```powershell
Get-Process             # list processes
Get-Service                # list services
Stop-Process -Name notepad     # kill a process
Get-ChildItem C:\               # list directory contents (alias: dir, ls)
Set-Location C:\Users              # change directory (alias: cd)
```

| Common verb | Meaning |
|---|---|
| Get | Retrieve information |
| Set | Change a setting |
| New | Create something |
| Remove | Delete something |
| Start / Stop | Control a running process/service |
| Invoke | Run/execute something |

## 3. The Object Pipeline

This is PowerShell's core differentiator: output isn't text, it's live **objects** with properties and methods, which downstream cmdlets can filter/sort/select directly instead of parsing text.

```powershell
Get-Process | Where-Object { $_.CPU -gt 50 } | Sort-Object CPU -Descending | Select-Object -First 5
Get-Service | Where-Object Status -eq "Running"
Get-ChildItem C:\ -Recurse | Where-Object Extension -eq ".exe"
```

| Cmdlet | Role |
|---|---|
| `Where-Object` | Filter objects by condition (like `grep`, but on properties, not text) |
| `Select-Object` | Choose specific properties, or first/last N objects |
| `Sort-Object` | Sort by a property |
| `ForEach-Object` | Run a block of code per object in the pipeline |
| `Format-Table` / `Format-List` | Control display formatting (doesn't change the underlying object) |

## 4. Getting Help

```powershell
Get-Help Get-Process
Get-Help Get-Process -Examples
Get-Help Get-Process -Full
Update-Help                        # download latest help content

Get-Command *service*             # discover cmdlets by keyword
Get-Member                           # inspect an object's properties/methods
Get-Process | Get-Member
```

`Get-Member` is one of the most important discovery tools in PowerShell — it reveals exactly what properties/methods any object exposes, critical for building pipelines against unfamiliar cmdlets.

## 5. Variables and Data Types

```powershell
$name = "Ansh"
$count = 42
$isAdmin = $true
$list = @("a", "b", "c")                  # array
$map = @{ Name = "Ansh"; Role = "Admin" }    # hashtable

$name.GetType()          # inspect the underlying .NET type
```

## 6. Providers — The Registry, Filesystem, and More as Drives

PowerShell exposes many data stores through a unified **provider** model, letting you `cd` into them like filesystems:

```powershell
Get-PSDrive                 # list all available "drives" (providers)

Set-Location HKLM:\Software\Microsoft\Windows\CurrentVersion\Run    # navigate the registry like a filesystem
Get-ChildItem Cert:\LocalMachine\My                                    # browse the certificate store
```

## 7. Aliases

PowerShell ships with aliases for common cmd.exe/Unix commands to ease the transition, but they resolve to real cmdlets underneath:

| Alias | Real cmdlet |
|---|---|
| `ls`, `dir` | `Get-ChildItem` |
| `cd` | `Set-Location` |
| `cat`, `type` | `Get-Content` |
| `rm`, `del` | `Remove-Item` |
| `ps` | `Get-Process` |
| `kill` | `Stop-Process` |
| `curl`, `wget` | `Invoke-WebRequest` (behavior differs subtly from real curl/wget) |

```powershell
Get-Alias                 # list all aliases
Get-Alias ls                 # resolve a specific alias
```

## 8. PowerShell vs cmd.exe — Practical Comparison

| Task | cmd.exe | PowerShell |
|---|---|---|
| List top 5 CPU processes | Not natively possible cleanly | `Get-Process \| Sort CPU -desc \| Select -First 5` |
| Parse structured data | Manual text parsing (`findstr`, `for /f`) | Native object properties |
| Remote execution | Not built-in | `Invoke-Command -ComputerName ...` (WinRM) |
| REST/API calls | Not built-in | `Invoke-RestMethod` |

## 9. Security Relevance

PowerShell's power is a double-edged sword — it's central to both legitimate administration and modern attacker tradecraft ("living off the land"):

| Concept | Relevance |
|---|---|
| Fileless malware | PowerShell can execute payloads entirely in memory, without dropping a file to disk |
| `-EncodedCommand` | Runs a Base64-encoded script — commonly used both legitimately (for special characters) and maliciously (obfuscation) |
| PowerShell logging | Script Block Logging and Module Logging (Group Policy-configurable) are key defensive telemetry — see [[Event_Log_Management]] |
| AMSI (Antimalware Scan Interface) | Lets AV/EDR inspect PowerShell script content dynamically, even if obfuscated, before execution |

```powershell
powershell -EncodedCommand <Base64String>       # commonly flagged in SOC alerting
powershell -ExecutionPolicy Bypass -File script.ps1
```

## 10. Interview Questions

1. What is PowerShell's core architectural difference from cmd.exe? → **It passes structured .NET objects through the pipeline instead of plain text**
2. What cmdlet would you use to discover what properties/methods an unfamiliar object exposes? → **`Get-Member`**
3. What does the provider model let you do with the registry? → **Navigate it like a filesystem (`HKLM:`, `HKCU:` as PSDrives) using the same `Get-ChildItem`/`Set-Location` cmdlets used for files**
4. Why is `-EncodedCommand` significant from a security monitoring perspective? → **It Base64-encodes the actual script content, which is legitimate for escaping special characters but is also commonly used to obfuscate malicious PowerShell from simple string-based detection**
5. What is AMSI, and why does it matter against obfuscated attacks? → **The Antimalware Scan Interface — lets AV/EDR inspect PowerShell (and other scripting engine) content dynamically at execution time, catching obfuscated payloads that static, on-disk scanning would miss**

## 11. Key Points

- PowerShell cmdlets follow **Verb-Noun** naming and communicate via a **structured object pipeline**, not text.
- `Get-Member` and `Get-Help` are the essential discovery tools for an unfamiliar cmdlet or object.
- The **provider model** exposes the registry, certificate store, and more as navigable "drives."
- PowerShell is simultaneously the standard modern **administration tool** and the dominant **post-exploitation tool** on Windows — logging (Script Block Logging) and AMSI are the key defensive controls.
- Scripting depth (functions, modules, remoting) is covered in [[PowerShell_Scripting]].

---
*Related: [[Command_Prompt_Basics]], [[PowerShell_Scripting]], [[WMI_and_CIM]], [[Event_Log_Management]]*
