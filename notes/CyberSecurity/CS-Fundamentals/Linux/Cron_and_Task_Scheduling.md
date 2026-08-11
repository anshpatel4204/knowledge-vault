## 1. Introduction

**Cron** is the traditional Linux time-based job scheduler, allowing commands or scripts to run automatically at specified times/intervals — backups, log rotation, maintenance scripts, and monitoring checks all commonly rely on it. It's also one of the most common **persistence mechanisms** attackers use to maintain access to a compromised system.

## 2. Crontab Syntax

```
* * * * *  command-to-execute
│ │ │ │ │
│ │ │ │ └── Day of week (0-6, Sunday=0, or names: sun,mon,tue...)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

| Symbol | Meaning |
|---|---|
| `*` | Every value (no restriction) |
| `,` | List of values (e.g., `1,15,30`) |
| `-` | Range (e.g., `1-5`) |
| `/` | Step values (e.g., `*/15` = every 15 units) |

## 3. Crontab Examples

```bash
0 2 * * *        /backup/script.sh          # every day at 2:00 AM
*/15 * * * *      /monitor/check.sh            # every 15 minutes
0 9 * * 1-5         /work/reminder.sh             # 9 AM on weekdays only
0 0 1 * *              /monthly/report.sh              # midnight on the 1st of every month
30 4 1,15 * *             /biweekly/task.sh                  # 4:30 AM on the 1st and 15th of the month
```

## 4. Managing User Crontabs

```bash
crontab -e             # edit your own crontab (opens in the default editor)
crontab -l                # list your current crontab entries
crontab -r                  # remove your entire crontab
crontab -u username -e         # edit another user's crontab (requires root)
```

Each user's crontab is stored under `/var/spool/cron/crontabs/<username>` (path varies slightly by distro).

## 5. System-Wide Cron

Beyond per-user crontabs, the system has its own scheduling locations:

| Location | Purpose |
|---|---|
| `/etc/crontab` | System-wide crontab — includes an extra **username field** before the command (since it can run as any user) |
| `/etc/cron.d/` | Directory for additional system-wide cron files, often installed by packages |
| `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` | Directories of scripts run automatically at the corresponding interval (driven by `run-parts` from `/etc/crontab`) |

```
# /etc/crontab format includes a user field
0 2 * * *  root  /usr/local/bin/backup.sh
```

## 6. Special Time Strings

```bash
@reboot     /path/to/script.sh      # run once at system startup
@daily        /path/to/script.sh       # equivalent to "0 0 * * *"
@weekly         /path/to/script.sh        # equivalent to "0 0 * * 0"
@monthly          /path/to/script.sh         # equivalent to "0 0 1 * *"
@yearly             /path/to/script.sh          # equivalent to "0 0 1 1 *"
```

`@reboot` is especially notable from a security perspective — it's a common way (legitimate or malicious) to ensure a script runs every time the system starts.

## 7. Environment Considerations in Cron

Cron jobs run in a **minimal environment**, without the interactive shell's full `PATH` or other environment variables set in `~/.bashrc` — a classic source of "works when I run it manually, but not in cron" bugs (see [[Environment_Variables_and_Shell_Config]]).

**Best practices:**
```bash
# Use absolute paths inside cron scripts
0 2 * * * /usr/bin/python3 /home/ansh/scripts/backup.py

# Or explicitly set PATH/environment at the top of the crontab
PATH=/usr/local/bin:/usr/bin:/bin
MAILTO=admin@example.com

# Redirect output to a log file to capture errors (cron mails output by default, if a mail system is configured)
0 2 * * * /path/to/script.sh >> /var/log/myscript.log 2>&1
```

## 8. at — One-Time Scheduled Jobs

Unlike cron's recurring schedule, `at` schedules a command to run **once** at a specific future time.

```bash
at 10:00 PM                  # enter interactive mode, then type commands, Ctrl+D to finish
echo "task" | at now + 1 hour     # schedule via pipe
atq                              # list pending at jobs
atrm 3                             # remove job number 3
```

## 9. systemd Timers — The Modern Alternative

See [[Systemd_and_Services]] for full detail — systemd timers (`.timer` units) are increasingly preferred over cron on modern distros, offering better logging integration, dependency management, and the ability to catch up on missed runs after downtime (`Persistent=true`).

```bash
systemctl list-timers      # view all active systemd timers
```

## 10. Cron as a Persistence and Privilege Escalation Vector

| Technique | Description |
|---|---|
| Malicious cron entries | Attackers add a cron job (often disguised with an innocuous-looking name) to maintain persistence after initial compromise |
| Writable cron scripts | If a cron job (especially one run by root) executes a script that's writable by a lower-privileged user, that user can edit the script to gain root-level command execution — a classic privilege escalation vector |
| PATH hijacking in cron scripts | If a cron script calls a command without an absolute path, and an attacker can write to an earlier directory in the effective `PATH`, they can plant a malicious binary that gets executed instead — see [[Linux_Privilege_Escalation]] |
| `@reboot` for persistence | Ensures a payload survives and re-executes after a reboot |

**Defensive/enumeration checks:**
```bash
# Audit all cron locations for unexpected entries
cat /etc/crontab
ls -la /etc/cron.d/ /etc/cron.hourly/ /etc/cron.daily/
for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l 2>/dev/null; done

# Check permissions on scripts referenced in cron jobs — should not be writable by non-owners
find / -writable -type f 2>/dev/null | grep -f <(grep -oP '(?<=\s)/\S+\.sh' /etc/crontab)
```

## 11. Interview Questions

1. What does the cron expression `0 2 * * *` mean? → **Run at 2:00 AM every day**
2. What's the difference between cron and `at`? → **Cron schedules recurring jobs; `at` schedules a one-time job for a specific future time**
3. Why do cron jobs often fail even though the same command works fine when run manually? → **Cron runs in a minimal environment without the interactive shell's full PATH/environment variables — always use absolute paths**
4. What does `@reboot` do in a crontab, and why is it security-relevant? → **Runs the command once at system startup — commonly abused by malware for persistence across reboots**
5. Why is a root-owned cron job that calls a world-writable script a privilege escalation risk? → **A lower-privileged user could edit that script to run arbitrary commands with root's privileges**
6. What's an advantage of systemd timers over traditional cron? → **Better logging integration, dependency management, and the ability to run missed jobs after downtime**

## 12. Key Points

- Cron syntax: **minute hour day-of-month month day-of-week command** — `*` means "every."
- User crontabs (`crontab -e`) vs system-wide locations (`/etc/crontab`, `/etc/cron.d/`, `/etc/cron.{hourly,daily,weekly,monthly}/`).
- Cron jobs run in a **minimal environment** — always use absolute paths and explicit PATH settings.
- **`at`** handles one-time scheduled jobs; **systemd timers** are the modern, more integrated alternative to cron.
- Cron is a **major persistence and privilege escalation vector** — auditing cron entries and the permissions of scripts they call is a standard security check.

---
*Related: [[Systemd_and_Services]], [[Linux_Privilege_Escalation]], [[Environment_Variables_and_Shell_Config]], [[Shell_Scripting]]*
