## 1. Introduction

**Hardening** a Linux system means reducing its attack surface and limiting the potential impact of a compromise through configuration, access control, and monitoring — going beyond default installation settings. This note consolidates the practical hardening techniques that build on everything else in this folder (permissions, users, firewalls, logging).

## 2. General Hardening Principles

- **Principle of Least Privilege** — users, services, and processes should have only the access they strictly need (see [[User_and_Group_Management]], [[File_Permissions]]).
- **Minimize attack surface** — remove/disable unused services, packages, and open ports.
- **Defense in depth** — layer multiple independent controls (firewall + service hardening + monitoring), so no single failure fully compromises the system.
- **Keep software patched** — see [[Package_Management]]; unpatched software remains one of the most common real-world breach causes.
- **Assume breach / plan for detection** — logging and monitoring (see [[Log_Management]]) matter as much as prevention.

## 3. SSH Hardening

SSH is the most common remote access point and a frequent brute-force target — hardening `/etc/ssh/sshd_config` is a top-priority baseline step.

```bash
# /etc/ssh/sshd_config
PermitRootLogin no                  # disable direct root login over SSH
PasswordAuthentication no             # require key-based auth only, disable password login
PubkeyAuthentication yes                # explicitly enable key-based auth
Port 2222                                 # non-default port (minor deterrent against automated scans, not real security on its own)
AllowUsers ansh deploy                      # explicit allow-list of permitted usernames
MaxAuthTries 3                                # limit login attempts per connection
Protocol 2                                      # ensure only the modern SSH protocol version is used
```

```bash
sudo systemctl restart sshd        # apply changes
```

## 4. Firewall Configuration (Brief — see [[Firewalls]] in Networking folder for concepts)

```bash
# ufw (Debian/Ubuntu-friendly)
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp        # only allow what's actually needed
sudo ufw enable

# firewalld (RHEL/Fedora)
sudo firewall-cmd --set-default-zone=drop
sudo firewall-cmd --add-service=ssh --permanent
sudo firewall-cmd --reload
```

**Default-deny** — explicitly allow only necessary traffic, block everything else by default.

## 5. Mandatory Access Control — SELinux and AppArmor

Beyond standard discretionary permissions (rwx/owner), **Mandatory Access Control (MAC)** systems enforce policy-defined restrictions that even root can't easily bypass — a significant additional containment layer if an application is compromised.

| System | Used By | Model |
|---|---|---|
| SELinux | RHEL/Fedora/CentOS | Label-based — every process and file has a security context; policies define allowed interactions between contexts |
| AppArmor | Ubuntu/Debian/SUSE | Path-based — profiles define what specific files/capabilities each application can access |

```bash
# SELinux
getenforce                  # check current mode (Enforcing, Permissive, Disabled)
sestatus                       # detailed status
setenforce 1                     # set to Enforcing (temporary, until reboot)
ls -Z /var/www/html                # view SELinux context on files

# AppArmor
sudo aa-status                  # show loaded profiles and their mode
sudo aa-enforce /etc/apparmor.d/usr.sbin.nginx     # set a profile to enforcing mode
sudo aa-complain /etc/apparmor.d/usr.sbin.nginx      # set to complain (log-only) mode
```

**Why this matters:** even if an attacker exploits a vulnerable web server to get code execution, a properly confined SELinux/AppArmor policy can prevent that process from reading unrelated files, opening arbitrary network connections, or executing unexpected binaries — containing the blast radius significantly.

## 6. fail2ban — Brute-Force Protection

Monitors log files (see [[Log_Management]]) for repeated authentication failures and automatically bans offending IPs (typically via firewall rules) for a configurable period.

```bash
sudo apt install fail2ban
sudo systemctl enable --now fail2ban
fail2ban-client status sshd          # check ban status for the SSH jail
fail2ban-client set sshd unbanip 203.0.113.5    # manually unban an IP
```

## 7. Kernel Hardening — sysctl Parameters

```bash
# /etc/sysctl.conf or /etc/sysctl.d/*.conf
net.ipv4.conf.all.rp_filter = 1        # enable reverse path filtering (anti-spoofing)
net.ipv4.tcp_syncookies = 1              # SYN flood protection
net.ipv4.conf.all.accept_redirects = 0     # ignore ICMP redirects (see [[ICMP]]) — prevents route manipulation
net.ipv4.icmp_echo_ignore_broadcasts = 1     # mitigate Smurf-style attacks
kernel.randomize_va_space = 2                  # full ASLR (Address Space Layout Randomization)
fs.suid_dumpable = 0                             # prevent SUID process core dumps (can leak sensitive memory)
```

```bash
sudo sysctl -p          # apply changes from a config file
sudo sysctl -a            # view all current kernel parameters
```

## 8. Minimizing Attack Surface

```bash
systemctl list-unit-files --state=enabled     # audit what's set to start at boot
sudo systemctl disable --now <unused-service>    # disable services you don't need

ss -tulnp                                          # audit listening ports — close anything unnecessary
```

**Removing unnecessary packages** (compilers, unused network services) on production systems reduces both attack surface and post-compromise tooling available to an attacker.

## 9. File Integrity Monitoring

Tools like **AIDE** or **Tripwire** create a baseline snapshot of critical file hashes/permissions and alert on unexpected changes — useful for detecting tampering (e.g., a backdoored binary or modified `/etc/passwd`).

```bash
sudo aideinit                    # initialize AIDE's baseline database
sudo aide --check                  # check current state against the baseline
```

## 10. Auditd for Security-Relevant Event Monitoring

See [[Log_Management]] for the full auditd introduction — used here specifically to watch high-value security targets:

```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_watch
sudo auditctl -w /etc/shadow -p wa -k shadow_watch
sudo auditctl -w /etc/sudoers -p wa -k sudoers_watch
```

## 11. Disk Encryption

Protects data at rest in case of physical theft or unauthorized disk access — **LUKS (Linux Unified Key Setup)** is the standard.

```bash
sudo cryptsetup luksFormat /dev/sdb1        # initialize an encrypted volume
sudo cryptsetup open /dev/sdb1 encrypted_vol   # unlock it
sudo mkfs.ext4 /dev/mapper/encrypted_vol         # format the now-decrypted device mapper path
```

## 12. Hardening Checklist Summary

| Area | Action |
|---|---|
| SSH | Disable root login, key-only auth, non-default port, `AllowUsers` |
| Firewall | Default-deny, allow only necessary ports |
| Access Control | SELinux/AppArmor enforcing mode |
| Brute-force protection | fail2ban on exposed services |
| Kernel parameters | SYN cookies, ASLR, anti-spoofing, disabled redirects |
| Attack surface | Disable unused services, close unused ports, remove unneeded packages |
| Integrity | File integrity monitoring (AIDE), auditd on critical files |
| Patching | Regular, automated where feasible (see [[Package_Management]]) |
| Encryption | LUKS full-disk/volume encryption for data at rest |
| Logging | Centralized/forwarded logs, journald persistence enabled |

## 13. Interview Questions

1. Why is disabling `PermitRootLogin` in SSH considered a baseline hardening step? → **Forces attackers to compromise a named user account AND escalate privileges separately, rather than directly brute-forcing/attacking the root account**
2. What's the difference between SELinux/AppArmor and standard Linux file permissions? → **They're Mandatory Access Control systems enforcing policy-defined restrictions beyond discretionary owner/group/others permissions — even root can be constrained**
3. What does fail2ban do? → **Monitors logs for repeated auth failures and automatically firewall-bans offending IPs**
4. Why is `net.ipv4.tcp_syncookies = 1` a useful sysctl hardening setting? → **Provides protection against SYN flood denial-of-service attacks**
5. What does LUKS provide? → **Full-disk/volume encryption, protecting data at rest from physical access/theft**
6. What's the purpose of file integrity monitoring tools like AIDE? → **Detecting unauthorized changes to critical system files by comparing against a known-good baseline**

## 14. Key Points

- Hardening applies **least privilege**, **minimized attack surface**, and **defense in depth** across SSH, firewall, access control, and monitoring layers.
- **SSH hardening** (key-only auth, no root login, allow-lists) is a top-priority baseline step given how commonly it's targeted.
- **SELinux/AppArmor** add Mandatory Access Control beyond standard file permissions, containing the blast radius of a compromised process.
- **fail2ban**, kernel **sysctl** tuning, and **file integrity monitoring (AIDE)** add layered detection/prevention.
- **LUKS** disk encryption protects data at rest; centralized logging (see [[Log_Management]]) ensures visibility survives a compromise.

---
*Related: [[User_and_Group_Management]], [[File_Permissions]], [[Log_Management]], [[Linux_Privilege_Escalation]], [[Firewalls]]*
