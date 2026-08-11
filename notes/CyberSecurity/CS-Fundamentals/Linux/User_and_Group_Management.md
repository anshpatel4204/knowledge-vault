## 1. Introduction

Linux is a **multi-user** operating system with a robust identity and access model built around **users** and **groups**. Every process, file, and permission check ultimately traces back to a user/group identity — making this foundational knowledge for both administration and security assessment (privilege escalation almost always involves manipulating or abusing this system).

## 2. Users — UID and Types

| Type | UID Range (typical) | Description |
|---|---|---|
| root | 0 | The superuser — bypasses virtually all permission checks |
| System/Service users | 1-999 (varies by distro) | Non-interactive accounts owning specific services (e.g., `www-data`, `mysql`, `sshd`) — should never have login shells |
| Regular users | 1000+ (typically) | Interactive human user accounts |

```bash
id                  # show current user's UID, GID, and group memberships
id ansh               # show info for a specific user
whoami                 # show current username
```

## 3. /etc/passwd — User Account Database

```
ansh:x:1000:1000:Ansh Patel:/home/ansh:/bin/bash
│    │ │    │    │          │           │
user pw UID  GID  comment    home dir    login shell
```

| Field                | Meaning                                                                                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Username             | Login name                                                                                                                                                          |
| Password placeholder | `x` means the real hash is in `/etc/shadow` (historically the actual hash lived here — a major early Unix security weakness, since `/etc/passwd` is world-readable) |
| UID                  | User ID                                                                                                                                                             |
| GID                  | Primary Group ID                                                                                                                                                    |
| GECOS                | Comment field (full name, etc.)                                                                                                                                     |
| Home directory       | e.g., `/home/ansh`                                                                                                                                                  |
| Login shell          | e.g., `/bin/bash`, or `/usr/sbin/nologin` for service accounts that shouldn't get an interactive shell                                                              |

## 4. /etc/shadow — Password Hashes

Stores actual password hashes and aging policy, readable **only by root** (unlike the world-readable `/etc/passwd`) — a critical security separation.

```
ansh:$6$randomsalt$hashvalue...:19000:0:99999:7:::
│    │                          │     │ │     │
user hash (algorithm$salt$hash) last-change min max warn
```

A leading `!` or `*` instead of a hash indicates the account is locked/has no password login. `$6$` indicates SHA-512 hashing (see [[Hashing]] in the Cryptography folder for hash algorithm detail).

```bash
sudo cat /etc/shadow      # requires root — this file itself being readable by a non-root user is a serious misconfiguration
```

## 5. /etc/group — Group Database

```
sudo:x:27:ansh,otheruser
│    │  │   │
group pw GID members
```

```bash
groups ansh              # show groups a user belongs to
cat /etc/group             # list all groups
```

## 6. Primary vs Secondary (Supplementary) Groups

| Type | Description |
|---|---|
| Primary Group | The default group assigned to files a user creates (set in `/etc/passwd`) |
| Secondary/Supplementary Groups | Additional groups a user belongs to, granting extra permissions (e.g., membership in `sudo`, `docker`, or `wheel` grants elevated capabilities) |

**Security note:** membership in certain groups is effectively equivalent to partial or full root access — e.g., the `docker` group can often be used to escalate to root (since Docker daemon operations run as root), and `disk` group members can potentially access raw block devices directly.

## 7. User Management Commands

```bash
useradd -m -s /bin/bash newuser        # create a user, with home directory (-m) and shell (-s)
usermod -aG sudo newuser                 # add to a supplementary group (-a = append, don't remove existing groups!)
usermod -s /usr/sbin/nologin svcuser        # change shell (e.g., to disable interactive login)
userdel -r newuser                            # delete a user AND their home directory
passwd newuser                                  # set/change a user's password
passwd -l newuser                                 # lock an account (prepends ! to the shadow hash)
chage -l ansh                                       # view password aging info
```

**Critical `usermod` gotcha:** forgetting `-a` when adding a group (`usermod -G sudo user` instead of `usermod -aG sudo user`) **replaces** all of a user's existing supplementary groups with just the new one — a very common, disruptive mistake.

## 8. Group Management Commands

```bash
groupadd developers          # create a group
groupmod -n newname oldname    # rename a group
groupdel developers              # delete a group
gpasswd -a ansh developers         # add a user to a group (alternative to usermod -aG)
```

## 9. su and sudo — Privilege Elevation

| Command | Behavior |
|---|---|
| `su` | Switch user — requires the **target** user's password (e.g., `su` alone requires the root password) |
| `su -` | Switch user with a full login environment (fresh shell, updated PATH/env vars) |
| `sudo` | Execute a single command as another user (default: root) — requires the **invoking user's own** password, governed by `/etc/sudoers` policy |

```bash
su - root
sudo apt update
sudo -u www-data whoami        # run a command as a specific non-root user
sudo -l                          # list what commands the current user is permitted to run via sudo
```

## 10. /etc/sudoers and Sudo Configuration

Controls exactly which users/groups can run which commands as which users, ideally edited only via **`visudo`** (which validates syntax before saving, preventing a broken sudoers file from locking out all sudo access).

```
# Format: user  host = (runas) commands
ansh    ALL=(ALL:ALL) ALL           # full sudo access
deploy  ALL=(www-data) /usr/bin/systemctl restart nginx    # limited: only this specific command
```

**Security relevance:** misconfigured sudoers entries (overly broad permissions, or allowing commands with known [GTFOBins](https://gtfobins.github.io/) escape techniques) are a primary privilege escalation vector — see [[Linux_Privilege_Escalation]].

## 11. Root vs Regular Users — The Core Security Boundary

| Aspect | root (UID 0) | Regular User |
|---|---|---|
| File permission checks | Bypassed entirely | Enforced per [[File_Permissions]] |
| Can bind to privileged ports (<1024) | Yes | No (without additional capabilities) |
| Can load kernel modules | Yes | No |
| Can change ownership of any file | Yes | No |

Any UID equal to 0 is effectively root, **regardless of username** — a common privilege escalation/backdoor technique is creating a second account with UID 0 that isn't named "root," which is why security audits specifically check `/etc/passwd` for any entry with UID 0.

```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd     # find all accounts with UID 0 — should only be "root"
```

## 12. Interview Questions

1. Where are password hashes actually stored, and why not in `/etc/passwd`? → **`/etc/shadow`, which is readable only by root — unlike the world-readable `/etc/passwd`, which historically stored hashes directly (a major weakness)**
2. What's the difference between `su` and `sudo`? → **`su` requires the target user's password to fully switch users; `sudo` requires the invoking user's own password to run a specific command as another user, per sudoers policy**
3. Why is forgetting the `-a` flag in `usermod -aG` dangerous? → **It replaces all existing supplementary group memberships instead of adding to them**
4. Why should `/etc/sudoers` only be edited with `visudo`? → **It validates syntax before saving, preventing a broken file from locking out all sudo access**
5. What UID does root always have, regardless of the account's name? → **UID 0**
6. Why is membership in the `docker` group effectively equivalent to root access? → **The Docker daemon runs as root, and containers can be used to access/mount the host filesystem, enabling privilege escalation**

## 13. Key Points

- `/etc/passwd` holds account info (world-readable); `/etc/shadow` holds password hashes (root-only readable) — a deliberate security separation.
- `/etc/group` defines groups; users have one **primary group** and any number of **supplementary groups**.
- `useradd`/`usermod`/`userdel` and `groupadd`/`groupmod`/`groupdel` manage accounts and groups — always use `usermod -aG`, never `-G` alone, when adding groups.
- **`su`** switches users fully (needs target's password); **`sudo`** runs one command as another user (needs your own password, governed by `/etc/sudoers`, edited via `visudo`).
- **UID 0 = root**, regardless of username — auditing for unexpected UID-0 accounts is a standard security check.
- Group memberships (`sudo`, `docker`, `disk`, etc.) can grant effective root access — a key privilege escalation enumeration target.

---
*Related: [[File_Permissions]], [[Linux_Privilege_Escalation]], [[Linux_Security_Hardening]]*
