## 1. Introduction

Windows ships a full set of native networking diagnostic and configuration tools, both in cmd.exe and PowerShell. These are essential for everyday administration and are also among the very first commands run in almost any Windows-based post-exploitation session for situational awareness — so fluency here matters for both blue and red perspectives.

## 2. IP Configuration

```cmd
ipconfig                        :: basic adapter IP info
ipconfig /all                       :: full details — MAC, DNS servers, DHCP lease info
ipconfig /release                       :: release current DHCP lease
ipconfig /renew                            :: request a new DHCP lease
ipconfig /flushdns                             :: clear the local DNS resolver cache
ipconfig /displaydns                              :: show cached DNS entries
```

```powershell
Get-NetIPConfiguration
Get-NetIPAddress
Get-DnsClientCache
Clear-DnsClientCache
```

## 3. Connectivity Testing

```cmd
ping 8.8.8.8                    :: ICMP echo test (see [[ICMP]])
ping -t 8.8.8.8                    :: continuous ping until stopped
tracert 8.8.8.8                       :: trace the route to a host
pathping 8.8.8.8                         :: combines ping + tracert, shows per-hop loss statistics
```

```powershell
Test-Connection 8.8.8.8
Test-NetConnection -ComputerName google.com -Port 443     # like a lightweight port-check tool
```

## 4. Connections and Listening Ports

```cmd
netstat -ano                    :: all connections/listeners, with PID (-o) and no name resolution (-n)
netstat -ab                        :: connections with owning executable (requires admin)
```

```powershell
Get-NetTCPConnection
Get-NetTCPConnection | Where-Object State -eq "Listen"
Get-NetTCPConnection -LocalPort 443
Get-Process -Id (Get-NetTCPConnection -LocalPort 443).OwningProcess
```

`netstat -ano` output columns: **Proto | Local Address | Foreign Address | State | PID** — cross-referencing the PID against `tasklist`/`Get-Process` is a standard first step when investigating a suspicious outbound connection.

## 5. Network Configuration — netsh

`netsh` is a powerful, script-friendly configuration tool covering firewall rules, interface settings, and more (largely superseded by PowerShell cmdlets for newer scenarios, but still very widely used):

```cmd
netsh interface ip show config                     :: show interface IP config
netsh advfirewall show allprofiles                     :: firewall status (see [[Windows_Security_Features]])
netsh advfirewall firewall add rule name="Allow80" dir=in action=allow protocol=TCP localport=80
netsh wlan show profiles                                   :: saved Wi-Fi profile names
netsh wlan show profile name="MyWifi" key=clear                :: recover a saved Wi-Fi password (requires admin)
```

## 6. Legacy `net` Commands

The `net` command family predates PowerShell and remains common in scripts and, notably, in attacker tooling due to its simplicity and ubiquity:

```cmd
net view                        :: list computers visible on the network
net view \\Server01                :: list shares on a specific computer
net use Z: \\Server01\Share            :: map a network drive
net use Z: /delete                        :: unmap a network drive
net share                                    :: list local shares (see [[SMB_and_Windows_Shares]])
net user /domain                                :: list domain users (from a domain-joined machine)
net group "Domain Admins" /domain                    :: list a domain group's members
net time \\dc01                                          :: query time from a domain controller
```

## 7. ARP and MAC-Level Info

```cmd
arp -a                     :: view the local ARP cache (see [[ARP]])
getmac /v                     :: view local MAC addresses per adapter
```

## 8. DNS Lookup Tools

```cmd
nslookup example.com                  :: basic DNS lookup
nslookup example.com 8.8.8.8              :: query a specific DNS server
```

```powershell
Resolve-DnsName example.com
Resolve-DnsName example.com -Type MX
```

## 9. Security/Reconnaissance Relevance

| Command | Why it's used early in reconnaissance |
|---|---|
| `ipconfig /all`, `net view`, `arp -a` | Map the local network segment and visible hosts |
| `netstat -ano` | Identify what's already listening/connecting on a compromised host |
| `net user /domain`, `net group "Domain Admins" /domain` | Enumerate domain accounts/privileged groups (see [[Active_Directory_Basics]]) |
| `netsh wlan show profile ... key=clear` | Recover stored Wi-Fi credentials |
| `nslookup`/`Resolve-DnsName` | Enumerate internal DNS records, sometimes revealing internal hostnames/services |

These are largely the same commands a legitimate administrator runs daily — which is exactly why baseline-aware detection (unusual timing, unusual account, unusual sequence) matters more than simply alerting on command names alone.

## 10. Interview Questions

1. What netstat flags show all connections with their owning PID, without DNS resolution? → **`netstat -ano`** (`-a` all, `-n` no name resolution, `-o` show owning process PID)
2. What's the PowerShell equivalent of `netstat -ano` for TCP connections? → **`Get-NetTCPConnection`**
3. How would you recover a saved Wi-Fi password from the command line? → **`netsh wlan show profile name="ProfileName" key=clear`** (requires administrative privileges)
4. What does `ipconfig /flushdns` do, and why might an admin (or attacker) use it? → **Clears the local DNS resolver cache — used to force fresh DNS resolution, e.g., after a DNS record change, or to clear traces of recently resolved malicious domains**
5. What legacy command family (`net view`, `net use`, `net group`) predates PowerShell but remains common in both admin scripts and attacker tooling? → **The `net` command family**

## 11. Key Points

- `ipconfig`, `ping`/`tracert`/`pathping`, and `netstat -ano` are the core connectivity/diagnostic commands, with `Get-NetIPConfiguration`/`Test-Connection`/`Get-NetTCPConnection` as PowerShell equivalents.
- `netsh` handles deeper configuration (firewall, Wi-Fi, interfaces) via a script-friendly syntax.
- The legacy `net` command family (`net view`, `net use`, `net user /domain`, `net group`) remains heavily used for both administration and network/domain reconnaissance.
- Nearly all of these commands are dual-use — legitimate daily administration and initial attacker situational awareness look identical at the command level, making **context and baselining** essential for detection.

---
*Related: [[Command_Prompt_Basics]], [[SMB_and_Windows_Shares]], [[Active_Directory_Basics]], [[ARP]]*
