## 1. Introduction

**Shodan** and **Censys** are internet-wide scanning search engines — instead of indexing web page content like Google, they continuously scan the entire IPv4 (and increasingly IPv6) address space and index what's actually running on every reachable port: banners, service versions, TLS certificates, and metadata. Querying either is a passive technique (see [[Passive_vs_Active_Recon]]) — you're searching *their* pre-existing scan data, not scanning the target yourself.

## 2. What They Index

| Data Point | Example Use |
|---|---|
| Open ports and services | Discovering exposed RDP, SSH, database, or industrial control system (ICS/SCADA) ports |
| Service banners | Version fingerprinting — e.g., an exposed Apache/nginx version string revealing known CVEs |
| TLS/SSL certificates | Finding all IPs presenting a certificate for a given organization's domain, even on non-standard hosts |
| Geolocation and ASN | Mapping which IP ranges/hosting providers/regions belong to the target |
| Default/exposed dashboards | Identifying exposed admin panels, webcams, IoT devices, and misconfigured cloud storage |

## 3. Useful Search Syntax

| Shodan Filter | Purpose |
|---|---|
| `org:"Example Corp"` | Search by organization name tied to IP allocation |
| `hostname:example.com` | Search by hostname |
| `port:3389` | Find hosts with a specific port open (e.g., RDP) |
| `ssl.cert.subject.cn:"example.com"` | Find hosts presenting a certificate for the domain, regardless of hostname |
| `net:203.0.113.0/24` | Search within a specific IP range |

Censys uses a similarly structured query language and additionally offers strong certificate-search capability, often used to cross-validate Shodan results.

## 4. Why This Matters More Than It First Appears

Because these platforms continuously scan the *entire* internet independent of any specific engagement, they frequently surface **shadow IT** — forgotten servers, legacy systems, or cloud instances the organization itself doesn't know are exposed. This is a recurring, high-value finding category precisely because it reflects gaps in the target's own asset inventory (echoing the [[Vulnerability_Management]] point that you can't secure what you don't know exists).

## 5. Ethical and Legal Note

Using Shodan/Censys to *search* is passive and not itself a violation — you're querying a third party's own published index. However, using the results to then *actively connect to or interact with* a discovered host that isn't confirmed in-scope crosses back into active, potentially unauthorized territory. Always cross-reference results against the confirmed scope before taking any further action.

## 6. Interview Questions

1. How is Shodan fundamentally different from a search engine like Google? → **It indexes internet-wide port/service scan data — banners, open ports, certificates — rather than crawled web page content**
2. Why is a Shodan or Censys search considered passive reconnaissance? → **You're querying the platform's own pre-existing scan database, not sending any traffic to the target directly**
3. What is "shadow IT," and why do Shodan/Censys frequently surface it? → **Unauthorized or forgotten systems the organization's own security team isn't aware of — these platforms' continuous, independent internet-wide scanning surfaces exposed assets regardless of whether the organization tracks them**
4. What ssl.cert-based search technique can reveal hosts you wouldn't find by hostname alone? → **Searching by the certificate's subject common name (e.g., `ssl.cert.subject.cn:"example.com"`) surfaces any IP presenting a cert for that domain, even on a hostname unrelated to it**

## 7. Key Points

- Shodan and Censys index **internet-wide scan data** (ports, banners, certificates), not web content — a fundamentally different, purely passive recon source.
- Certificate-based search is especially powerful for finding infrastructure that hostname-based search would miss entirely.
- These tools frequently surface **shadow IT** — exposed assets the target organization itself may not know about.
- Searching is passive; **actively connecting** to a discovered host not confirmed in-scope crosses back into active, potentially unauthorized territory — always cross-check against the RoE first.

---
*Related: [[Passive_vs_Active_Recon]], [[WHOIS_and_DNS_Recon]], [[Vulnerability_Management]], [[Network_Footprinting]]*
