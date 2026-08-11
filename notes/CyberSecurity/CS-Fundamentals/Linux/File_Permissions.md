## 1. Introduction

Linux enforces access control through a **permissions system** attached to every file and directory, defining who can **read**, **write**, and **execute** it. This is the foundational access control mechanism referenced constantly in both system administration and offensive security (privilege escalation almost always exploits a permissions misconfiguration somewhere).

## 2. The Permission Triad

Every file/directory has three permission sets, for three categories of users:

| Category | Symbol | Applies To |
|---|---|---|
| Owner (User) | `u` | The user who owns the file |
| Group | `g` | Members of the file's assigned group |
| Others | `o` | Everyone else |

Each category has three possible permissions:

| Permission | Symbol | Numeric Value | Effect on Files | Effect on Directories |
|---|---|---|---|---|
| Read | `r` | 4 | View file contents | List directory contents |
| Write | `w` | 2 | Modify file contents | Create/delete/rename files within it |
| Execute | `x` | 1 | Run as a program/script | Enter the directory (`cd` into it) |

## 3. Reading `ls -l` Output

```bash
-rwxr-xr--  1 ansh staff  4096 Aug 11 10:00 script.sh
```

```
-    rwx     r-x     r--
│    │       │       │
Type Owner   Group   Others
```

- `-` → regular file (see [[File_Types_and_Inodes]] for all type symbols).
- Owner (`ansh`): read, write, execute.
- Group (`staff`): read, execute (no write).
- Others: read only.

## 4. Numeric (Octal) Permission Notation

Each permission triad can be expressed as a single digit by summing values (r=4, w=2, x=1):

| Combination | Value | Meaning |
|---|---|---|
| rwx | 7 | Read + Write + Execute |
| rw- | 6 | Read + Write |
| r-x | 5 | Read + Execute |
| r-- | 4 | Read only |
| --- | 0 | No permissions |

So `rwxr-xr--` = **754** (owner=7, group=5, others=4).

## 5. chmod — Changing Permissions

```bash
chmod 755 script.sh          # numeric: rwxr-xr-x
chmod u+x script.sh          # symbolic: add execute for owner
chmod g-w file.txt           # remove write from group
chmod o=r file.txt           # set others to read-only exactly
chmod -R 644 /some/dir       # recursive
```

**Symbolic operators:** `+` (add), `-` (remove), `=` (set exactly). Targets: `u` (user/owner), `g` (group), `o` (others), `a` (all).

## 6. chown and chgrp — Changing Ownership

```bash
chown ansh file.txt              # change owner
chown ansh:staff file.txt         # change owner AND group
chgrp staff file.txt              # change group only
chown -R ansh:staff /some/dir     # recursive
```

Only **root** (or the current owner, in limited cases) can change a file's ownership — an ordinary user cannot give away files they don't own to someone else.

## 7. umask — Default Permission Masking

**umask** defines which permission bits are **removed** by default when new files/directories are created, rather than defining what's granted directly.

| Default max permission | Common umask | Resulting permission |
|---|---|---|
| Files: 666 (rw-rw-rw-) | 022 | 644 (rw-r--r--) |
| Directories: 777 (rwxrwxrwx) | 022 | 755 (rwxr-xr-x) |

```bash
umask            # display current umask
umask 027         # set a stricter umask (removes group-write, all-others access)
```

Note files never get execute permission by default (even with umask 000) — execute must be explicitly added afterward; this default applies only to directories and explicitly-executable programs at creation.

## 8. Special Permissions — SUID, SGID, Sticky Bit

Beyond the basic rwx triad, three special permission bits significantly affect behavior — and are a **major focus in privilege escalation** (see [[Linux_Privilege_Escalation]]).

### SUID (Set User ID) — `s` in the owner's execute position

When set on an **executable**, the program runs with the **privileges of the file's owner**, not the user who launched it — regardless of who executes it.

```bash
chmod u+s /usr/bin/passwd     # example: passwd needs SUID to write to /etc/shadow as root
ls -l /usr/bin/passwd
-rwsr-xr-x  ...  /usr/bin/passwd
```

**Security risk:** a SUID binary owned by root with an exploitable flaw (or an unsafely configured custom SUID script) lets any user who can execute it gain root-level actions — auditing SUID binaries is a standard privilege escalation enumeration step:

```bash
find / -perm -4000 -type f 2>/dev/null    # find all SUID binaries
```

### SGID (Set Group ID) — `s` in the group's execute position

On an **executable**, runs with the privileges of the file's **group**. On a **directory**, newly created files inside automatically inherit the **directory's group** (instead of the creating user's primary group) — useful for shared team directories.

```bash
chmod g+s /shared/project_dir
find / -perm -2000 -type f 2>/dev/null    # find all SGID binaries
```

### Sticky Bit — `t` in the others' execute position

On a **directory**, restricts file deletion so that only the file's **owner** (or root) can delete/rename it, even if others have write access to the directory. Classic example: `/tmp`, which is world-writable but uses the sticky bit to prevent users from deleting each other's files.

```bash
chmod +t /some/shared/dir
ls -ld /tmp
drwxrwxrwt  ...  /tmp        ← note the "t" at the end
```

## 9. Special Permission Numeric Notation

A fourth leading digit represents special permissions: SUID=4, SGID=2, Sticky=1 (summed if combined).

```bash
chmod 4755 file    # SUID + rwxr-xr-x
chmod 2755 dir      # SGID + rwxr-xr-x
chmod 1777 dir      # Sticky + rwxrwxrwx (like /tmp)
```

## 10. Access Control Lists (ACLs) — Beyond the Basic Triad

The standard owner/group/others model only supports **one** owner and **one** group per file. **ACLs** extend this to grant fine-grained permissions to multiple specific users/groups on a single file.

```bash
getfacl file.txt                       # view ACLs
setfacl -m u:someuser:rwx file.txt      # grant a specific user rwx
setfacl -m g:someteam:rx file.txt       # grant a specific group r-x
```

## 11. Directory Permissions — A Common Point of Confusion

Directory permissions behave differently from file permissions in an important way: **execute (`x`) on a directory is required to access/traverse into it or any file within it**, even if you have read permission on the file itself. Having `r` on a directory without `x` lets you list filenames but not access their contents or metadata.

## 12. Interview Questions

1. What does chmod 750 mean in symbolic notation? → **rwxr-x--- (owner: full, group: read+execute, others: none)**
2. What does the SUID bit do when set on an executable? → **The program runs with the privileges of the file's owner, regardless of who executes it**
3. Why is `/tmp` both world-writable and protected from users deleting each other's files? → **The sticky bit restricts deletion to only the file's owner (or root), even though the directory itself is writable by everyone**
4. What's the difference between SUID and SGID? → **SUID runs an executable as its owner; SGID runs it as its group (or, on directories, makes new files inherit the directory's group)**
5. Why would a penetration tester run `find / -perm -4000` during enumeration? → **To locate SUID binaries, which are a common privilege escalation vector**
6. Why might a user with read permission on a file still be unable to access it? → **They may lack execute permission on the containing directory, which is required to traverse into it**

## 13. Key Points

- Permissions = **read/write/execute** for **owner/group/others**, expressed symbolically (`rwxr-xr--`) or numerically (`754`).
- **chmod** changes permissions; **chown/chgrp** change ownership; **umask** sets default permissions for new files.
- **SUID** runs a program as its owner; **SGID** runs as its group (or sets group inheritance on directories); the **sticky bit** restricts deletion in shared directories.
- SUID/SGID binaries are a **primary privilege escalation target** — always audited during Linux security assessments.
- **ACLs** extend beyond the basic owner/group/others model for fine-grained, multi-user permission control.

---
*Related: [[File_Types_and_Inodes]], [[User_and_Group_Management]], [[Linux_Privilege_Escalation]]*
