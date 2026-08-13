## 1. Introduction

Domain-level reconnaissance is usually the first concrete step once a target scope is confirmed — **WHOIS** reveals who registered a domain and how, while **DNS enumeration** reveals the actual technical infrastructure behind it. Both build directly on DNS concepts from the Networking folder (see [[DNS]]).

## 2. WHOIS Lookups

WHOIS is a public database protocol returning registration details for a domain or IP block.

| Field | Reveals |
|---|---|
| Registrant/Admin/Tech contact | Organization name, sometimes named individuals (frequently redacted by privacy services today) |
| Registrar | Which company the domain is registered through |
| Creation/Expiration date | Domain age — useful for spotting recently-registered lookalike/phishing domains |
| Name servers | Points toward the DNS/hosting provider in use |

**Note:** GDPR and similar privacy regulations have significantly reduced how much personal registrant data WHOIS exposes today compared to a decade ago — organizational and infrastructure data is still generally available, but named individuals often aren't.

## 3. Core DNS Record Types for Recon

| Record | Reveals |
|---|---|
| A / AAAA | IPv4/IPv6 address the domain resolves to |
| MX | Mail server infrastructure (often reveals if email is hosted via Google Workspace, Microsoft 365, or self-hosted) |
| NS | Authoritative nameservers — reveals DNS/hosting provider |
| TXT | Often contains SPF/DKIM records (email security posture), domain verification strings for third-party services in use |
| CNAME | Aliases — can reveal additional subdomains or third-party services (e.g., a CNAME pointing to a cloud provider) |

## 4. DNS Zone Transfer (AXFR) — A Misconfiguration Worth Checking

If a DNS server is misconfigured to allow unrestricted **zone transfers**, an attacker can request the server's *entire* zone file in one query — every subdomain, every record, in one shot. This is a legacy misconfiguration that's increasingly rare on properly hardened servers but remains a high-value, quick check.

```
dig axfr @<nameserver> <domain>
```

A successful zone transfer against a live target is technically an **active** recon technique (it directly queries the target's own nameserver) — see [[Passive_vs_Active_Recon]].

## 5. Passive DNS and Certificate Transparency

Rather than querying the target's live DNS servers, **passive DNS** databases (built from historical internet-wide DNS query logs collected by third parties) let you see historical DNS records without touching the target at all — genuinely passive. Similarly, **Certificate Transparency (CT) logs** (public logs of every SSL/TLS certificate issued) often reveal subdomains that were never intended to be public, simply because a certificate was issued for them — a key input into [[Subdomain_Enumeration]].

## 6. Interview Questions

1. What does an MX record reveal during domain recon? → **The organization's mail server infrastructure — often revealing whether email is hosted via a third-party provider like Google Workspace/Microsoft 365 or self-hosted**
2. Why is a successful DNS zone transfer (AXFR) considered a high-value finding? → **It returns the entire DNS zone file in a single query — every subdomain and record at once — due to a server misconfiguration that shouldn't allow unrestricted transfers**
3. Why has WHOIS become less useful for finding named individuals over the past decade? → **GDPR and similar privacy regulations have driven widespread adoption of WHOIS privacy/redaction services, hiding personal registrant details while organizational/infrastructure data often remains visible**
4. How do Certificate Transparency logs help with subdomain discovery, and why is checking them passive? → **Every issued SSL/TLS certificate is publicly logged, including the subdomain(s) it covers, often revealing subdomains never meant to be public — checking third-party CT log archives doesn't touch the target's own infrastructure at all**

## 7. Key Points

- **WHOIS** reveals domain registration/ownership data (increasingly redacted for individuals due to privacy law); **DNS enumeration** reveals actual technical infrastructure.
- Key record types for recon: **A/AAAA** (IPs), **MX** (mail infrastructure), **NS** (hosting/DNS provider), **TXT** (email security, service verification), **CNAME** (aliases/third-party services).
- A misconfigured **zone transfer (AXFR)** can leak an entire DNS zone in one query — a classic, high-value, quick check, but technically an active technique.
- **Passive DNS databases** and **Certificate Transparency logs** enable fully passive historical DNS/subdomain discovery, directly feeding into [[Subdomain_Enumeration]].

---
*Related: [[DNS]] (CS-Fundamentals/Networking), [[Passive_vs_Active_Recon]], [[Subdomain_Enumeration]], [[Google_Dorking]]*
