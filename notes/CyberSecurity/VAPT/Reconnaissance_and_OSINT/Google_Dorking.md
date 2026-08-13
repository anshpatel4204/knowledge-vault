## 1. Introduction

**Google Dorking** (also called Google Hacking) uses advanced search engine operators to surface specific, often sensitive, publicly indexed content that isn't easily found through a normal search — exposed login portals, misconfigured directory listings, leaked documents, and more. It's entirely passive (see [[Passive_vs_Active_Recon]]) — every result was already indexed by the search engine itself, nothing is sent to the target directly.

## 2. Core Search Operators

| Operator | Function | Example |
|---|---|---|
| `site:` | Restrict results to a specific domain | `site:example.com` |
| `filetype:` | Restrict to a specific file extension | `filetype:pdf site:example.com` |
| `intitle:` | Search within page titles | `intitle:"index of"` |
| `inurl:` | Search within the URL itself | `inurl:admin site:example.com` |
| `intext:` | Search within page body text | `intext:"password" filetype:log` |
| `-` (minus) | Exclude a term from results | `site:example.com -inurl:www` |
| `"..."` | Exact phrase match | `"internal use only" filetype:pdf` |

## 3. Common Dork Combinations and What They Find

```
site:example.com filetype:pdf                → publicly indexed PDF documents
intitle:"index of" site:example.com           → exposed directory listings
inurl:login site:example.com                  → discovering login portals
site:example.com filetype:env                 → occasionally exposed .env config files
site:pastebin.com "example.com"               → leaked data mentioning the target
inurl:wp-admin site:example.com               → WordPress admin panels
```

## 4. The Google Hacking Database (GHDB)

A community-maintained, categorized archive of proven dork queries (hosted historically via Exploit-DB) — organized by what each dork tends to reveal (files containing usernames, vulnerable servers, sensitive directories, error messages that leak stack traces, and more). Rather than inventing dorks from scratch, checking the GHDB against a target's domain is usually the efficient starting point.

## 5. Limitations and Caveats

| Limitation | Detail |
|---|---|
| Only surfaces indexed content | If a search engine hasn't crawled/indexed a page, dorking won't find it — doesn't replace active enumeration |
| Rate limiting | Search engines throttle automated/rapid dork queries — manual, paced querying is more reliable |
| Cached vs live | A dork may surface a cached page reflecting content that's since been removed/fixed — always verify against the live site before reporting a finding |

## 6. Interview Questions

1. Why is Google Dorking considered a passive reconnaissance technique? → **It only queries search engine indexes that already exist — no request is ever sent directly to the target's own infrastructure**
2. What does the `filetype:` operator do, and give an example use case. → **Restricts results to a specific file extension — e.g., `filetype:pdf site:example.com` to find publicly indexed PDF documents belonging to the target**
3. What is the Google Hacking Database (GHDB)? → **A community-maintained, categorized archive of proven dork queries organized by what each one tends to reveal, used as an efficient starting point rather than inventing dorks from scratch**
4. Why should a dorking result always be verified against the live site before being reported as a finding? → **Search results can reflect a cached, outdated version of a page — content may have already been removed or fixed since the page was last indexed**

## 7. Key Points

- Google Dorking uses **advanced search operators** (`site:`, `filetype:`, `intitle:`, `inurl:`, `intext:`) to surface sensitive indexed content, entirely passively.
- The **GHDB** is a curated library of proven dork queries by category — the practical starting point over inventing dorks from scratch.
- Results are limited to **already-indexed** content and may be stale — always verify against the live target before reporting.
- A purely passive technique — no interaction with the target's own systems occurs at any point.

---
*Related: [[Passive_vs_Active_Recon]], [[OSINT_Framework_and_Tools]], [[WHOIS_and_DNS_Recon]]*
