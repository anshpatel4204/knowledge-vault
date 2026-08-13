## 1. Introduction

**cmd.exe** (Command Prompt) is Windows' original text-based shell, dating back to OS/2 and carried forward through every NT release for backward compatibility with MS-DOS-era batch scripts. It's largely superseded by [[PowerShell_Basics]] for serious administration, but it's still everywhere — in legacy scripts, in `RUN` dialogs (`Win+R`), and as a first foothold in many attacker command chains, so fluency with it remains essential.

## 2. Launching and Basics

```cmd
cmd                     :: launch a new cmd instance
cmd /c command             :: run a command and exit
cmd /k command                :: run a command and stay open
help                              :: list built-in commands
command /?                          :: help for a specific command
```

## 3. Navigation and File Operations

```cmd
cd C:\Users\Ansh          :: change directory
cd ..                        :: go up one level
dir                             :: list directory contents
dir /a                            :: list including hidden/system files
dir /s                               :: list recursively

copy file.txt D:\Backup\      :: copy a file
xcopy folder D:\Backup\ /E /I    :: copy a folder recursively
robocopy C:\Src D:\Dst /MIR          :: robust copy — mirrors a directory tree (preferred over xcopy)
move file.txt D:\                       :: move a file
del file.txt                               :: delete a file
rmdir /s /q folder                            :: delete a folder recursively, no prompt
mkdir NewFolder                                  :: create a folder
ren old.txt new.txt                                 :: rename a file
```

## 4. Viewing and Searching

```cmd
type file.txt                       :: print file contents (like `cat`)
more file.txt                          :: paginated view
findstr /i "error" logfile.txt            :: search text in a file (like `grep`)
find "text" file.txt                         :: simpler substring search
where notepad.exe                               :: locate an executable in PATH (like `which`)
```

## 5. System Information Commands

```cmd
systeminfo                     :: full OS/hardware summary
hostname                          :: computer name
whoami                               :: current user
whoami /priv                            :: current user's privileges
whoami /groups                             :: current user's group memberships
ver                                           :: Windows version
tasklist                                        :: running processes (see [[Windows_Processes_and_Services]])
```

## 6. Networking Commands (Preview)

Covered fully in [[Windows_Networking_Commands]]:

```cmd
ipconfig /all
ping 8.8.8.8
netstat -ano
```

## 7. Batch Scripting Fundamentals

`.bat`/`.cmd` files let cmd.exe execute a sequence of commands, with basic control flow:

```bat
@echo off
set NAME=Ansh
echo Hello, %NAME%!

if exist C:\temp\file.txt (
    echo File exists
) else (
    echo File not found
)

for %%f in (*.txt) do (
    echo Processing %%f
)

:loop
echo Looping...
goto loop
```

| Element | Purpose |
|---|---|
| `@echo off` | Suppresses command echoing (cleaner output) |
| `%VAR%` | Variable reference |
| `%1`, `%2`... | Positional script arguments |
| `if` / `for` / `goto` | Basic control flow (no real functions — labels + `goto` instead) |

## 8. Redirection and Chaining

```cmd
command > output.txt         :: redirect stdout, overwrite
command >> output.txt           :: redirect stdout, append
command 2> errors.txt              :: redirect stderr
command1 && command2                  :: run command2 only if command1 succeeds
command1 || command2                     :: run command2 only if command1 fails
command1 & command2                         :: run both regardless
command1 | command2                            :: pipe output (text-based, unlike PowerShell's object pipeline)
```

## 9. cmd.exe vs PowerShell

| Aspect | cmd.exe | PowerShell |
|---|---|---|
| Pipeline data | Plain text | .NET **objects** (see [[PowerShell_Basics]]) |
| Scripting power | Minimal (batch files) | Full scripting language |
| Remoting | None built-in | WinRM-based remoting |
| Legacy compatibility | Maximum — original DOS-era commands | Backward-compatible via aliases, but a different paradigm |
| Common attacker use | Still widely used for simple one-liners, LOLBins | Preferred for complex post-exploitation tooling |

## 10. Interview Questions

1. What's the key structural difference between cmd.exe's pipeline and PowerShell's? → **cmd.exe pipes plain text between commands; PowerShell pipes structured .NET objects**
2. What does `robocopy /MIR` do, and why is it generally preferred over `xcopy`? → **Mirrors a directory tree (making destination match source exactly, including deletions); it's more robust, resumable, and logging-capable than xcopy**
3. How do you check the current user's privileges from cmd.exe? → **`whoami /priv`**
4. What does `&&` do differently from `&` when chaining commands? → **`&&` only runs the next command if the previous one succeeded (exit code 0); `&` runs it unconditionally**
5. Why does fluency in cmd.exe still matter despite PowerShell being more powerful? → **It remains common in legacy scripts, quick one-liners, and as an initial foothold shell in many exploitation/post-exploitation scenarios**

## 11. Key Points

- **cmd.exe** is Windows' original DOS-descended shell — text-pipeline based, limited scripting via `.bat` files.
- Core file operations (`copy`, `move`, `del`, `dir`) and info commands (`whoami`, `systeminfo`, `tasklist`) are foundational and still commonly seen.
- **`robocopy`** is the modern, robust replacement for `xcopy` for directory copies/mirrors.
- cmd.exe's biggest limitation vs PowerShell is its **plain-text pipeline** and minimal scripting language — see [[PowerShell_Basics]] for the modern alternative.

---
*Related: [[Windows_Basics]], [[PowerShell_Basics]], [[Windows_Networking_Commands]], [[Windows_Processes_and_Services]]*
