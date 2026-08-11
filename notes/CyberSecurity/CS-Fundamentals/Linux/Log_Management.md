## 1. Introduction

Linux logs nearly everything of significance — authentication attempts, kernel events, service activity, application errors — primarily under **`/var/log`**. Understanding what lives where, and how to query it via `journalctl` or classic syslog tools, is fundamental for troubleshooting and absolutely central to SOC/incident response work.

## 2. /var/log — Key Log Files

| Log File | Contents |
|---|---|
| `/var/log/syslog` (Debian/Ubuntu) or `/var/log/messages` (RHEL/CentOS) | General system activity log |
| `/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL/CentOS) | Authentication events — logins, sudo usage, SSH attempts |
| `/var/log/kern.log` | Kernel-specific messages |
| `/var/log/dmesg` | Boot-time kernel ring buffer messages |
| `/var/log/faillog` / `/var/log/btmp` | Failed login attempts |
| `/var/log/wtmp` | Login records (viewed via `last`) |
| `/var/log/lastlog` | Most recent login per user |
| `/var/log/cron` | Cron job execution logs (RHEL-style; Debian logs cron activity within syslog) |
| `/var/log/apt/` or `/var/log/yum.log` / `/var/log/dnf.log` | Package installation/update history |
| `/var/log/apache2/` or `/var/log/nginx/` | Web server access/error logs |
| `/var/log/audit/audit.log` | Detailed security auditing (if `auditd` is installed — see below) |

## 3. Viewing Login History

```bash
last                # successful login history (from wtmp)
last -a               # show hostname in the last column
lastb                  # failed login attempts (from btmp) — requires root
lastlog                  # last login time per user account
w                          # currently logged-in users and their activity
who                          # currently logged-in users, simpler output
```

## 4. journalctl — systemd's Centralized Log Viewer

Modern systemd-based distros centralize logs into a structured binary format managed by **journald**, queried via `journalctl` (see [[Systemd_and_Services]]).

```bash
journalctl                       # view all logs (oldest first)
journalctl -r                      # reverse — newest first
journalctl -u sshd                   # logs for a specific unit/service
journalctl -f                          # follow live, like tail -f
journalctl -b                            # logs since the current boot
journalctl -b -1                           # logs from the previous boot
journalctl --since "2024-01-01" --until "2024-01-02"    # time range
journalctl -p err                            # filter by priority (emerg, alert, crit, err, warning, notice, info, debug)
journalctl _PID=1234                            # logs from a specific process ID
journalctl -k                                     # kernel messages only (like dmesg)
```

**Persistence:** by default, the journal may be stored only in memory/volatile storage on some distros (lost on reboot) unless `/var/log/journal/` exists and persistent storage is enabled in `/etc/systemd/journald.conf` (`Storage=persistent`) — an important consideration for forensic readiness.

## 5. Classic Syslog (rsyslog/syslog-ng)

Even on systemd systems, many distros still run **rsyslog**, which reads from journald and writes traditional flat-text log files to `/var/log/`, following standard **syslog severity levels**:

| Level | Number | Meaning |
|---|---|---|
| Emergency | 0 | System unusable |
| Alert | 1 | Immediate action required |
| Critical | 2 | Critical condition |
| Error | 3 | Error condition |
| Warning | 4 | Warning condition |
| Notice | 5 | Normal but significant |
| Info | 6 | Informational |
| Debug | 7 | Debug-level detail |

Configuration lives in `/etc/rsyslog.conf` and `/etc/rsyslog.d/`, defining which **facility** (auth, cron, kern, mail, etc.) and severity combinations route to which log files.

## 6. auditd — Detailed Security Auditing

**auditd** (the Linux Audit Daemon) provides fine-grained, security-focused logging beyond standard syslog — capable of tracking specific syscalls, file access, and command execution at a level detailed enough for compliance (PCI-DSS, etc.) and deep forensic investigation.

```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_changes    # watch a file for writes/attribute changes, tag with a key
ausearch -k passwd_changes                                  # search audit logs by that key
aureport --summary                                            # summarized audit report
```

## 7. Log Rotation — logrotate

Logs would grow indefinitely and eventually fill the disk without rotation — **logrotate** automatically compresses, archives, and eventually deletes old logs based on policy.

```bash
# /etc/logrotate.d/myapp
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```

| Directive | Meaning |
|---|---|
| `daily`/`weekly`/`monthly` | Rotation frequency |
| `rotate N` | Keep N old versions before deleting |
| `compress` | Gzip rotated logs |
| `missingok` | Don't error if the log file is missing |
| `notifempty` | Don't rotate an empty log file |

```bash
logrotate -d /etc/logrotate.conf     # dry-run/debug mode
```

## 8. Log Analysis Techniques (Tying Back to Earlier Notes)

Combine [[Text_Processing_Commands]] and [[Regular_Expressions]] for practical log analysis:

```bash
# Count failed SSH login attempts by IP
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn

# Find all sudo command usage
grep "sudo" /var/log/auth.log | grep "COMMAND"

# Live-monitor a web server log, filtering for errors
tail -f /var/log/nginx/error.log | grep -i "error"

# Search across all logs for a specific IP
grep -r "203.0.113.5" /var/log/
```

## 9. Log Security — Integrity and Tampering

| Concern | Description | Mitigation |
|---|---|---|
| Local log tampering | An attacker with root access can edit/delete local logs to cover their tracks | Forward logs to a remote, centralized log server (SIEM) in near real-time — by the time an attacker thinks to clean up, the evidence has already left the host |
| Log injection | Attacker-controlled input (e.g., a crafted username or HTTP header) written unsanitized into logs, potentially breaking log parsing or injecting fake entries | Proper input sanitization before logging |
| Insufficient logging | Critical events (auth failures, privilege escalation, config changes) not logged at all, leaving blind spots | Enable auditd rules for security-critical files/syscalls, ensure auth/sudo logging is active |
| Journal not persistent | Volatile-only journald storage loses all logs on reboot, destroying forensic evidence | Set `Storage=persistent` in `/etc/systemd/journald.conf` |

## 10. Interview Questions

1. Where does SSH authentication activity typically get logged? → **`/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL/CentOS)**
2. What command shows logs for a specific systemd service, following live? → **`journalctl -u <service> -f`**
3. Why might journald logs disappear after a reboot? → **If persistent storage isn't enabled (`Storage=persistent` in journald.conf), the journal may only be kept in volatile memory**
4. What does logrotate solve? → **Prevents logs from growing indefinitely by automatically archiving, compressing, and eventually deleting old log files**
5. Why is forwarding logs to a remote server considered a security best practice? → **It prevents an attacker with local root access from tampering with or deleting logs to cover their tracks**
6. What tool provides detailed, syscall-level security auditing beyond standard syslog? → **auditd**

## 11. Key Points

- Key logs live under **`/var/log`**: `auth.log`/`secure` (authentication), `syslog`/`messages` (general), service-specific logs.
- **`journalctl`** is the modern centralized query tool for systemd-based logging; classic **rsyslog** still writes flat-text logs by severity/facility.
- **auditd** provides fine-grained, security-focused auditing (file access, syscalls) beyond standard logging.
- **logrotate** prevents unbounded log growth via scheduled archiving/compression/deletion.
- **Remote log forwarding** is a critical defense against attackers tampering with local logs to cover their tracks — a core SIEM/SOC concept.

---
*Related: [[Systemd_and_Services]], [[Text_Processing_Commands]], [[Regular_Expressions]], [[Linux_Security_Hardening]]*
