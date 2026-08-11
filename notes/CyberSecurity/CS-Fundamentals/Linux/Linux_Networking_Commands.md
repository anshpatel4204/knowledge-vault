## 1. Introduction

This note catalogs the essential command-line tools for network configuration, diagnostics, and file transfer on Linux — the practical toolkit that connects everything covered conceptually in the **Networking** folder to real command-line usage.

## 2. Viewing Network Interfaces and IPs

```bash
ip addr show           # modern standard — show all interfaces and their IPs
ip a                       # shorthand
ifconfig                     # legacy tool (deprecated but still common), same basic purpose
ip link show                   # show interfaces without IP details (link-layer state)
hostname -I                      # quick way to show just this host's IP addresses
```

`ip` (from the `iproute2` suite) is the modern replacement for the older `net-tools` package (`ifconfig`, `netstat`, `route`), which is deprecated on many distros but still widely present.

## 3. Configuring Interfaces (Temporary/Runtime)

```bash
sudo ip addr add 192.168.1.100/24 dev eth0      # assign an IP temporarily
sudo ip link set eth0 up                            # bring an interface up
sudo ip link set eth0 down                            # bring an interface down
sudo ip route add default via 192.168.1.1               # set a default gateway
```

Permanent configuration lives in distro-specific config files (Netplan on modern Ubuntu, `/etc/network/interfaces` on older Debian, NetworkManager config on many desktop distros).

## 4. Viewing Routing Table

```bash
ip route show          # display the routing table
ip r                       # shorthand
route -n                     # legacy equivalent, numeric output
```

## 5. Connections and Listening Ports

```bash
ss -tulnp              # modern standard: TCP/UDP, listening, numeric, show process
netstat -tulnp            # legacy equivalent (may need installing on modern distros)
```

| Flag | Meaning |
|---|---|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Listening sockets only |
| `-n` | Numeric (don't resolve hostnames/services — faster, avoids DNS lookups) |
| `-p` | Show the owning process (requires root for full visibility) |

```bash
ss -tan               # all TCP connections (not just listening)
ss -s                    # summary statistics
```

## 6. Testing Connectivity

```bash
ping 8.8.8.8                   # basic reachability test (ICMP echo — see [[ICMP]])
ping -c 4 example.com             # send exactly 4 pings then stop
traceroute example.com              # map the path to a destination
mtr example.com                       # combines ping + traceroute, continuously updating
```

## 7. DNS Lookup Tools

```bash
nslookup example.com
dig example.com
dig example.com MX              # query a specific record type
dig -x 8.8.8.8                    # reverse lookup
host example.com                    # simpler alternative
getent hosts example.com               # resolve using the system's configured resolution methods (includes /etc/hosts)
```

See [[DNS]] in the Networking folder for the underlying protocol detail.

## 8. HTTP/File Transfer Tools

```bash
curl https://example.com                    # fetch a URL, print to stdout
curl -O https://example.com/file.zip           # download and save with the remote filename
curl -I https://example.com                       # headers only (HEAD request)
curl -X POST -d "key=value" https://example.com/api    # send a POST request with data
wget https://example.com/file.zip                          # simpler, download-focused alternative to curl
wget -r https://example.com/                                  # recursive download (mirror a site)
```

**curl vs wget:** `curl` is more versatile for scripting/API interaction (supports more protocols, request types, and outputs to stdout by default); `wget` is more convenient for straightforward recursive downloads and has built-in retry/resume behavior.

## 9. Remote Access — SSH

```bash
ssh user@host                    # connect
ssh -p 2222 user@host               # connect on a non-default port
ssh -i ~/.ssh/id_ed25519 user@host    # specify a private key
ssh -L 8080:localhost:80 user@host      # local port forwarding — tunnel remote port 80 to local 8080
ssh -D 1080 user@host                     # dynamic port forwarding (SOCKS proxy through the SSH tunnel)
ssh-keygen -t ed25519                       # generate a new keypair
ssh-copy-id user@host                         # copy your public key to a remote host's authorized_keys
```

## 10. Secure File Transfer — scp, sftp, rsync

```bash
scp file.txt user@host:/remote/path/          # copy a file to a remote host
scp user@host:/remote/file.txt ./               # copy a file from a remote host
scp -r localdir user@host:/remote/path/           # recursive directory copy

sftp user@host                                       # interactive secure FTP session

rsync -avz localdir/ user@host:/remote/path/            # efficient sync, only transfers differences
rsync -avz --delete localdir/ user@host:/remote/path/      # mirror exactly, deleting extra remote files
```

`rsync` is generally preferred over `scp` for anything beyond one-off transfers — it's far more efficient for repeated syncs (only transfers changed portions of files) and supports resuming interrupted transfers.

## 11. Network Interface Statistics and Packet Capture

```bash
tcpdump -i eth0                       # capture packets on an interface
tcpdump -i eth0 port 80                  # filter by port
tcpdump -i eth0 -w capture.pcap            # write to a file for later analysis (e.g., in Wireshark)
tcpdump -i eth0 host 192.168.1.5              # filter by host

nmap -sn 192.168.1.0/24               # host discovery scan across a subnet
nmap -p 1-1000 192.168.1.10              # scan a port range on a target
```

`tcpdump` and `nmap` cross firmly into VAPT/security-tooling territory but are fundamentally built on the same Linux networking command-line foundation.

## 12. Firewall Management (Brief — see [[Firewalls]] in Networking folder for concepts)

```bash
# iptables (legacy but still widely used)
sudo iptables -L -v -n                   # list current rules

# ufw (Uncomplicated Firewall — Debian/Ubuntu friendly frontend)
sudo ufw status
sudo ufw allow 22/tcp
sudo ufw enable

# firewalld (RHEL/Fedora)
sudo firewall-cmd --list-all
sudo firewall-cmd --add-port=22/tcp --permanent
sudo firewall-cmd --reload
```

## 13. Interview Questions

1. What command replaced the legacy `ifconfig`/`netstat`/`route` tools on modern Linux? → **The `ip` command (from iproute2) and `ss` for socket statistics**
2. What's the difference between `curl` and `wget`? → **`curl` is more versatile for scripting/APIs and various protocols; `wget` is simpler and better suited for recursive/resumable downloads**
3. Why is `rsync` generally preferred over `scp` for repeated file synchronization? → **It only transfers changed portions of files and supports resuming interrupted transfers, far more efficient**
4. What does `ssh -L 8080:localhost:80 user@host` do? → **Local port forwarding — tunnels connections to local port 8080 through SSH to port 80 on the remote host**
5. What flags would you use with `ss` to see all listening TCP and UDP ports with the owning process? → **`ss -tulnp`**
6. What tool would you use to capture raw network packets for later analysis in Wireshark? → **`tcpdump`, with the `-w` flag to write a pcap file**

## 14. Key Points

- **`ip`** (interfaces/routing) and **`ss`** (sockets/ports) are the modern standard tools, replacing legacy `ifconfig`/`netstat`/`route`.
- **`ping`**, **`traceroute`**, **`dig`/`nslookup`** cover basic connectivity and DNS diagnostics.
- **`curl`**/**`wget`** for HTTP/file retrieval; **`scp`**/**`sftp`**/**`rsync`** for secure file transfer (prefer `rsync` for repeated syncs).
- **`ssh`** supports key-based auth and port forwarding (local/dynamic tunneling) beyond simple remote shells.
- **`tcpdump`** and **`nmap`** bridge everyday networking commands into security/VAPT tooling territory.

---
*Related: [[DNS]], [[Firewalls]], [[ICMP]], [[Ports]], [[Linux_Security_Hardening]]*
