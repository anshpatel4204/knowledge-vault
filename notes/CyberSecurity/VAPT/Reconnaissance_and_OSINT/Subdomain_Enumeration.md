## 1. Introduction

Most organizations have a far larger attack surface than their main domain suggests — staging environments, forgotten marketing microsites, internal tools accidentally exposed, acquired-company domains. **Subdomain enumeration** systematically discovers this hidden surface, and is usually the single highest-value recon activity by volume of new attack surface uncovered per hour spent.

## 2. Passive Subdomain Discovery

| Technique | How It Works |
|---|---|
| Certificate Transparency logs | Search CT log aggregators (e.g., crt.sh) for every certificate ever issued mentioning the domain — see [[WHOIS_and_DNS_Recon]] |
| Search engine dorking | `site:*.example.com` and similar — see [[Google_Dorking]] |
| Passive DNS databases | Historical DNS resolution data from third-party aggregators |
| Public datasets | Common Crawl, Rapid7's Project Sonar (Forward/Reverse DNS datasets) |

## 3. Active Subdomain Discovery

| Technique | How It Works |
|---|---|
| DNS brute-forcing | Querying the target's DNS directly against a wordlist of common subdomain names (e.g., `dev`, `staging`, `api`, `vpn`, `mail`) |
| Zone transfer attempt | See [[WHOIS_and_DNS_Recon]] — a direct, active query against the target's nameserver |
| Virtual host discovery | Sending HTTP requests with different `Host:` headers to a discovered IP, revealing subdomains sharing the same server |

## 4. Common Automation Tools

| Tool | Approach |
|---|---|
| Sublist3r | Aggregates results from multiple passive sources in one run |
| Amass | Combines passive sources, active brute-forcing, and DNS resolution in a single, more comprehensive framework |
| Subfinder | Fast, passive-source-focused enumeration, commonly chained with other tools |
| Assetfinder | Lightweight, quick passive discovery, often used as a first pass |

A common real-world workflow chains several tools together and deduplicates the combined output, since no single source is fully comprehensive on its own.

## 5. After Discovery — Validation

Discovered subdomain names alone aren't useful until validated:

```
1. Resolve each subdomain to confirm it's actually live (not a stale DNS record)
2. Probe HTTP(S) response on live hosts to fingerprint what's actually running
3. Screenshot live web interfaces at scale for fast visual triage (e.g., via EyeWitness/Aquatone)
4. Flag anything unexpected — staging/dev environments, exposed admin panels, forgotten services
```

## 6. Interview Questions

1. Why is subdomain enumeration often considered the highest-value recon activity by attack surface uncovered? → **Organizations typically have far more subdomains (staging, dev, forgotten microsites, internal tools) than their main domain suggests, and each one is a potential new entry point**
2. Give one passive and one active subdomain discovery technique. → **Passive: searching Certificate Transparency logs (e.g., via crt.sh); Active: DNS brute-forcing against a wordlist of common subdomain names**
3. Why do real-world workflows typically chain multiple enumeration tools together rather than relying on one? → **No single source or tool is fully comprehensive — combining and deduplicating output from several (e.g., Amass, Subfinder, Assetfinder) yields more complete coverage**
4. After discovering a list of subdomain names, what's the next necessary step before they're useful findings? → **Validation — resolving each to confirm it's live, then probing/fingerprinting and screenshotting to identify what's actually running and flag anything unexpected**

## 7. Key Points

- Subdomains represent an organization's **real, often much larger attack surface** beyond the primary domain — enumeration systematically maps it.
- **Passive** techniques (CT logs, dorking, passive DNS) carry no detection risk; **active** techniques (brute-forcing, zone transfers, vhost discovery) carry more yield but touch the target directly.
- Tools like Amass, Subfinder, Sublist3r, and Assetfinder are commonly **chained together** since no single source is comprehensive.
- Raw subdomain lists must be **validated** (resolved, probed, screenshotted) before they become actionable findings — feeds directly into [[Network_Footprinting]].

---
*Related: [[WHOIS_and_DNS_Recon]], [[Google_Dorking]], [[Passive_vs_Active_Recon]], [[Network_Footprinting]]*
