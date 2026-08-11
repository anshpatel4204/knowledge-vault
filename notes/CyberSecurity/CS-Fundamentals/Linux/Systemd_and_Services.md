## 1. Introduction

**systemd** is the modern init system and service manager used by most major Linux distributions (Ubuntu, Debian, Fedora, RHEL, Arch). As **PID 1** (see [[Linux_Boot_Process]] and [[Process_Lifecycle_and_IPC]]), it's the ancestor of every other process and is responsible for starting, stopping, monitoring, and managing dependencies between system services.

## 2. Why systemd Replaced SysVinit

| Aspect | SysVinit (legacy) | systemd |
|---|---|---|
| Startup | Sequential, numbered scripts (`/etc/init.d/`) | Parallelized, dependency-based |
| Boot speed | Slower (services wait in sequence) | Faster (independent services start simultaneously) |
| Service definition | Shell scripts | Declarative unit files |
| Dependency management | Manual, script-based | Built-in, explicit dependency graph |
| Logging | Scattered across `/var/log/` files | Centralized via `journald` |

## 3. systemctl — The Core Command

```bash
systemctl status sshd            # show status of a service
systemctl start sshd               # start a service now
systemctl stop sshd                  # stop a service now
systemctl restart sshd                 # stop then start
systemctl reload sshd                    # reload config without a full restart (if supported)
systemctl enable sshd                      # start automatically at boot
systemctl disable sshd                       # don't start at boot
systemctl enable --now sshd                    # enable AND start immediately
systemctl is-active sshd                         # quick check: active or not
systemctl is-enabled sshd                          # quick check: enabled at boot or not
systemctl list-units --type=service                  # list all loaded services
systemctl list-unit-files                               # list all known unit files and their enabled state
```

## 4. Unit Types

systemd manages more than just traditional services — it uses the general concept of a **unit**:

| Unit Type | Suffix | Purpose |
|---|---|---|
| Service | `.service` | A managed process/daemon |
| Socket | `.socket` | Network/IPC socket, can trigger starting a service on first connection (socket activation) |
| Timer | `.timer` | Scheduled activation, a modern alternative to cron (see [[Cron_and_Task_Scheduling]]) |
| Mount | `.mount` | A filesystem mount point |
| Target | `.target` | A grouping/synchronization point (replaces SysVinit runlevels) |
| Device | `.device` | Represents a kernel-exposed device |
| Path | `.path` | Triggers activation based on filesystem path changes |

## 5. Anatomy of a Service Unit File

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target
Requires=postgresql.service

[Service]
Type=simple
User=myappuser
ExecStart=/usr/bin/myapp --config /etc/myapp/config.yaml
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

| Section | Purpose |
|---|---|
| `[Unit]` | Metadata and dependency ordering (`After`, `Before`, `Requires`, `Wants`) |
| `[Service]` | How to actually run the service (`ExecStart`, `User`, `Restart` policy) |
| `[Install]` | Determines behavior when `enable`d (which target activates it) |

```bash
sudo systemctl daemon-reload         # required after editing/adding a unit file, so systemd re-reads it
```

## 6. Targets (Replacing Runlevels)

See [[Linux_Boot_Process]] for the full runlevel-to-target mapping. Key targets:

```bash
systemctl get-default              # show current default target
systemctl set-default multi-user.target
systemctl isolate rescue.target      # switch to a target immediately (e.g., for maintenance)
```

## 7. journalctl — Centralized Logging

systemd's logging component, **journald**, collects logs from all services into a centralized, structured binary log queried with `journalctl` (see [[Log_Management]] for full detail).

```bash
journalctl -u sshd              # logs for a specific service/unit
journalctl -f                     # follow logs live (like tail -f)
journalctl -b                       # logs from the current boot
journalctl --since "1 hour ago"       # time-filtered logs
journalctl -p err                       # filter by priority (errors and above)
```

## 8. systemd Timers — Modern Cron Alternative

```ini
# /etc/systemd/system/backup.timer
[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

Paired with a corresponding `backup.service` unit defining what actually runs. Advantages over cron: integrates with systemd dependency/logging, can run missed jobs on next boot (`Persistent=true`), more precise/flexible scheduling syntax.

```bash
systemctl list-timers        # view all active timers and their next run time
```

## 9. Masking vs Disabling

```bash
systemctl disable sshd        # won't start automatically at boot, but CAN still be started manually
systemctl mask sshd             # creates a symlink to /dev/null, making it IMPOSSIBLE to start (even manually) until unmasked
systemctl unmask sshd
```

**Masking** is a stronger control — used when a service must be absolutely prevented from running (including accidental manual starts or being pulled in as a dependency by another unit).

## 10. Security Relevance

| Concern | Description |
|---|---|
| Malicious/persistence services | Attackers create rogue `.service` units for persistence — auditing `systemctl list-unit-files` and comparing against a known baseline is a standard incident response step |
| Running services as root unnecessarily | A service unit without an explicit `User=` directive runs as root by default — services should specify a least-privilege dedicated user wherever possible |
| Hardening directives | systemd supports sandboxing options directly in unit files: `NoNewPrivileges=true`, `ProtectSystem=strict`, `PrivateTmp=true`, `ReadOnlyPaths=`, restricting what a service can access even if compromised |
| journald log tampering | Centralized logs are a target for attackers attempting to cover tracks — forwarding logs to a remote/immutable log server mitigates local tampering (see [[Log_Management]]) |

```bash
# Example hardening additions in a [Service] block
NoNewPrivileges=true
ProtectSystem=strict
PrivateTmp=true
ProtectHome=true
```

## 11. Interview Questions

1. What process is systemd, in terms of PID? → **PID 1 — the first process started by the kernel, ancestor of all others**
2. What command must you run after editing a unit file for changes to take effect? → **`systemctl daemon-reload`**
3. What's the difference between `systemctl disable` and `systemctl mask`? → **Disable prevents auto-start at boot but allows manual starting; mask makes it impossible to start at all (even manually) until unmasked**
4. What replaced SysVinit runlevels in systemd? → **Targets**
5. What's an advantage of systemd timers over traditional cron? → **Integration with systemd logging/dependencies, and the ability to run missed jobs after a reboot (`Persistent=true`)**
6. Why is it a security best practice to specify a `User=` directive in a service unit? → **Without it, the service runs as root by default — least privilege reduces the impact if the service is compromised**

## 12. Key Points

- **systemd** is PID 1 on most modern distros, managing services via declarative **unit files** rather than sequential shell scripts.
- **systemctl** is the primary control command: `start`/`stop`/`restart`/`enable`/`disable`/`status`.
- **Targets** replace SysVinit runlevels; **timers** offer a modern alternative to cron.
- **`journalctl`** provides centralized, structured logging across all systemd-managed services.
- **Masking** (vs disabling) provides a stronger guarantee a service cannot run; unit-file hardening directives (`NoNewPrivileges`, `ProtectSystem`) reduce blast radius if a service is compromised.

---
*Related: [[Linux_Boot_Process]], [[Cron_and_Task_Scheduling]], [[Log_Management]], [[Process_Management]]*
