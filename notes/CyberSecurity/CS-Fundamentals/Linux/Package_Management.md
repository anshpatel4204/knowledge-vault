## 1. Introduction

A **package manager** automates installing, updating, configuring, and removing software along with its dependencies, replacing manual downloads and compilation. Each major Linux distribution family standardizes on a package format and manager, making this one of the most immediately noticeable differences between distros.

## 2. Package Management by Distro Family

| Family | Package Format | Low-Level Tool | High-Level Tool |
|---|---|---|---|
| Debian-based (Debian, Ubuntu, Kali) | `.deb` | `dpkg` | `apt` / `apt-get` |
| Red Hat-based (RHEL, Fedora, CentOS) | `.rpm` | `rpm` | `dnf` (modern) / `yum` (legacy) |
| Arch-based (Arch, Manjaro) | Custom tarball | — | `pacman` |
| SUSE-based (openSUSE) | `.rpm` | `rpm` | `zypper` |
| Alpine | `.apk` | — | `apk` |

**High-level vs low-level:** low-level tools (`dpkg`, `rpm`) install/manage individual package files directly but don't resolve dependencies automatically; high-level tools (`apt`, `dnf`) wrap them, adding automatic dependency resolution and repository management.

## 3. APT (Debian/Ubuntu/Kali)

```bash
sudo apt update                    # refresh the local package index from repositories
sudo apt upgrade                     # upgrade all installed packages to latest versions
sudo apt install nmap                  # install a package
sudo apt remove nmap                     # remove a package, keep config files
sudo apt purge nmap                        # remove a package AND its config files
sudo apt autoremove                          # remove orphaned dependencies no longer needed
apt search keyword                              # search available packages
apt show nmap                                     # show package details
apt list --installed                                # list installed packages
```

**`apt` vs `apt-get`:** `apt` is a newer, more user-friendly frontend combining the most commonly used features of `apt-get` and `apt-cache`; `apt-get` remains more script-stable (its output format is guaranteed not to change, useful for automation).

## 4. dpkg — Low-Level Debian Package Tool

```bash
sudo dpkg -i package.deb        # install a local .deb file directly (no dependency resolution)
dpkg -l                           # list installed packages
dpkg -L nmap                        # list files installed by a package
dpkg -S /usr/bin/nmap                 # find which package owns a given file
```

If `dpkg -i` fails due to missing dependencies, `sudo apt install -f` (fix broken) resolves and installs them.

## 5. YUM/DNF (RHEL/Fedora/CentOS)

```bash
sudo dnf install nmap          # install a package
sudo dnf update                   # update all packages
sudo dnf remove nmap                # remove a package
dnf search keyword                    # search packages
dnf info nmap                          # show package details
dnf list installed                       # list installed packages
sudo dnf history                           # view transaction history (useful for rollback)
```

**DNF** is the modern successor to **YUM**, with better dependency resolution — command syntax is largely backward-compatible (`yum install` still works as an alias on most systems that migrated to DNF).

## 6. RPM — Low-Level Red Hat Package Tool

```bash
sudo rpm -ivh package.rpm      # install (verbose, show hash progress)
rpm -qa                          # list all installed packages
rpm -qf /usr/bin/nmap              # find which package owns a file
rpm -qi nmap                         # show package info
```

## 7. Pacman (Arch Linux)

```bash
sudo pacman -Syu             # sync repos and upgrade the whole system
sudo pacman -S nmap             # install a package
sudo pacman -R nmap               # remove a package
pacman -Ss keyword                  # search
pacman -Qi nmap                       # show info about an installed package
```

## 8. Package Repositories

Package managers pull from configured **repositories** (repos) — remote (or local) collections of packages.

```bash
# Debian/Ubuntu — repository list
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

# RHEL/Fedora — repository list
cat /etc/yum.repos.d/*.repo
```

**PPAs (Personal Package Archives)** on Ubuntu let users add third-party repositories for software not in the official repos — convenient, but a security consideration, since it extends trust to whoever maintains that PPA.

```bash
sudo add-apt-repository ppa:someuser/someppa
```

## 9. Verifying Package Integrity

Package managers verify downloaded packages against cryptographic signatures from trusted repository keys before installation, protecting against tampered/malicious packages (see [[Digital_Signatures]] in the Cryptography folder for the underlying mechanism).

```bash
apt-key list                        # (legacy) list trusted APT signing keys
gpg --list-keys                       # list GPG keys generally
rpm --checksig package.rpm              # verify an RPM's signature
```

## 10. Compiling from Source

When a package isn't available through a repository, software can be built manually from source code — the traditional Unix workflow:

```bash
./configure          # check dependencies, generate a Makefile
make                    # compile the source code
sudo make install         # install the compiled binaries (typically to /usr/local)
```

**Trade-offs:** more control and access to the latest/custom versions, but no automatic dependency tracking, no easy uninstall/updates via the package manager, and greater responsibility on the admin to track security patches manually.

## 11. Containerized/Universal Package Formats (Brief Mention)

Beyond distro-native package managers, newer cross-distro formats have emerged: **Snap** (Ubuntu/Canonical), **Flatpak** (desktop-focused, sandboxed), and **AppImage** (single portable executable, no installation needed) — these bundle dependencies with the application itself, trading some disk space/duplication for consistency across distros.

## 12. Security Considerations

| Concern | Description |
|---|---|
| Unsigned/untrusted repositories | Adding third-party repos (PPAs, random `.deb`/`.rpm` sources) extends trust and expands attack surface |
| Outdated packages | Failing to regularly run `apt update && apt upgrade` (or the DNF equivalent) leaves known vulnerabilities unpatched |
| Supply chain attacks | Compromised upstream packages/dependencies can affect anyone who installs them — a growing real-world concern across all package ecosystems |
| Package confusion/typosquatting | Malicious packages with names similar to popular ones, tricking users/scripts into installing the wrong thing |

## 13. Interview Questions

1. What package format and manager does Ubuntu use? → **`.deb` packages, managed via `apt`/`dpkg`**
2. What's the difference between `apt` and `dpkg`? → **`dpkg` is the low-level tool with no automatic dependency resolution; `apt` is the high-level wrapper that resolves dependencies and manages repositories**
3. What command finds which installed package owns a specific file, on Debian-based systems? → **`dpkg -S /path/to/file`**
4. What replaced YUM as the modern package manager on Fedora/RHEL? → **DNF**
5. How does a package manager verify a downloaded package hasn't been tampered with? → **By checking its cryptographic signature against trusted repository signing keys**
6. What's a security trade-off of adding a third-party PPA/repository? → **You're extending trust to whoever maintains that repository, expanding your system's attack surface**

## 14. Key Points

- Debian-based distros use **APT/dpkg** (`.deb`); Red Hat-based use **DNF/YUM/RPM** (`.rpm`); Arch uses **pacman**.
- High-level tools (apt, dnf, pacman) handle dependency resolution and repositories; low-level tools (dpkg, rpm) manage individual package files directly.
- Packages are cryptographically signed and verified against trusted repository keys before installation.
- Compiling from source (`./configure && make && make install`) is the fallback when no package is available, at the cost of manual dependency/update tracking.
- Keeping packages updated (`apt update && upgrade` / `dnf update`) is one of the single most impactful basic security hygiene practices.

---
*Related: [[Linux_Basics]], [[Linux_Security_Hardening]], [[Systemd_and_Services]]*
