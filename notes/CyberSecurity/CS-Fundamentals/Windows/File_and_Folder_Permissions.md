## 1. Introduction

Windows enforces file and folder access through **NTFS Access Control Lists (ACLs)** — a far more granular model than the traditional Linux rwx bits (see [[File_Permissions]]). Every securable object (files, folders, registry keys, even processes) has a **Security Descriptor** containing an owner, a group, and a list of access rules. Understanding this model is essential for both correct system administration and privilege-escalation analysis.

## 2. The Security Descriptor

Every NTFS object's Security Descriptor contains:

| Component | Description |
|---|---|
| Owner SID | The account that owns the object (see [[Users_Groups_and_SID]]) |
| Primary Group SID | Rarely used on Windows (POSIX-subsystem legacy) |
| DACL (Discretionary ACL) | The actual permission rules — who can/cannot do what |
| SACL (System ACL) | Auditing rules — what access attempts get logged |

## 3. DACL — Access Control Entries (ACEs)

The **DACL** is an ordered list of **Access Control Entries (ACEs)**, each either **Allow** or **Deny**, tied to a specific SID (user/group) and a set of permissions.

| ACE evaluation rule | Behavior |
|---|---|
| Order matters | ACEs are evaluated top-to-bottom; the first matching Deny or Allow for the requested access wins |
| Explicit Deny beats inherited Allow | An explicit Deny ACE always overrides an inherited Allow, regardless of list order in modern Windows ACL editors |
| No matching ACE | Access is implicitly denied |

## 4. Standard NTFS Permissions

| Permission | Meaning |
|---|---|
| Full Control | Everything below, plus change permissions/take ownership |
| Modify | Read, write, execute, and delete |
| Read & Execute | Read content and run executables |
| List Folder Contents | (Folders only) enumerate contents |
| Read | View content/attributes |
| Write | Create files/write content |

These map onto more granular special permissions (e.g., "Take Ownership," "Change Permissions," "Delete Subfolders and Files") visible via the **Advanced Security Settings** dialog or `icacls`.

## 5. Inheritance

By default, a new file/folder **inherits** permissions from its parent folder. Inherited ACEs are marked distinctly from explicit ones, and disabling inheritance on an object either converts inherited ACEs to explicit ones or removes them, depending on the chosen option.

```
Parent Folder (explicit ACEs)
    └── Child Folder (inherits parent's ACEs + can add its own explicit ones)
            └── File (inherits from Child Folder)
```

## 6. Command-Line Permission Management

```cmd
:: view permissions on a file/folder
icacls C:\Data

:: grant a user Modify rights, with inheritance
icacls C:\Data /grant Ansh:(OI)(CI)M

:: remove a user's permissions
icacls C:\Data /remove Ansh

:: reset to inherited defaults
icacls C:\Data /reset

:: take ownership of a file (common in privilege escalation / forensics)
takeown /f C:\Data\secret.txt
```

**PowerShell equivalents:**

```powershell
Get-Acl C:\Data | Format-List
$acl = Get-Acl C:\Data
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Ansh","Modify","Allow")
$acl.SetAccessRule($rule)
Set-Acl C:\Data $acl
```

## 7. Ownership

Every object has an **owner**, who always implicitly has the right to read and modify permissions (Take Ownership / Change Permissions) even without an explicit ACE granting it — this is a deliberate design so an admin can never be permanently locked out of an object they own.

```cmd
takeown /f file.txt /a         :: give ownership to the built-in Administrators group
```

**Security relevance:** `SeTakeOwnershipPrivilege` and `SeRestorePrivilege`, if held by a compromised low-privilege account, can be abused to seize ownership of protected files/services and escalate privileges — a well-known Windows privesc technique (see [[Windows_Privilege_Escalation]]).

## 8. Effective Permissions vs Share Permissions

When a folder is accessed over the network via an SMB share (see [[SMB_and_Windows_Shares]]), **two** permission layers apply, and the more restrictive of the two wins:

| Layer | Scope |
|---|---|
| Share permissions | Apply only to network access via that specific share |
| NTFS permissions | Apply to the object itself, both locally and over the network |

A common misconfiguration: administrators set generous Share permissions (e.g., Everyone: Full Control) assuming NTFS ACLs will restrict access properly — but if NTFS ACLs are also loose, this becomes an over-permissioned share, a frequent finding in internal pentests.

## 9. Interview Questions

1. What's the difference between a DACL and a SACL? → **The DACL controls who can access an object (permissions); the SACL controls what access attempts get audited/logged**
2. If a user has an inherited Allow permission and an explicit Deny, which wins? → **The explicit Deny always overrides an inherited Allow**
3. What happens if network Share permissions and local NTFS permissions on the same folder conflict? → **The more restrictive of the two effective permissions applies**
4. Why can an object's owner always change its permissions, even without an explicit ACE granting it? → **Ownership implicitly grants Take Ownership/Change Permissions rights — by design, so admins can never be permanently locked out**
5. What privilege, if held by a low-privileged attacker, enables seizing ownership of protected files as a privilege-escalation technique? → **`SeTakeOwnershipPrivilege`** (often alongside `SeRestorePrivilege` / `SeBackupPrivilege`)

## 10. Key Points

- Windows permissions use **ACLs made of ordered ACEs (Allow/Deny)**, evaluated with explicit Deny always beating inherited Allow.
- Permissions **inherit** down the folder tree by default, and can be broken/converted at any level.
- `icacls`/`Get-Acl`/`Set-Acl` are the primary CLI tools; `takeown` seizes ownership.
- Network shares layer **Share permissions on top of NTFS permissions** — the more restrictive wins, and mismatches here are a common misconfiguration.
- Ownership always implies the right to modify permissions, which is also a real privilege-escalation vector when abused.

---
*Related: [[NTFS_File_System]], [[Users_Groups_and_SID]], [[SMB_and_Windows_Shares]], [[Windows_Privilege_Escalation]]*
