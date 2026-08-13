## 1. Introduction

**OSINT (Open-Source Intelligence)** is information gathering exclusively from publicly available sources — no hacking, no unauthorized access, just organizing what's already public into a usable target profile. Nearly every technique in the rest of this folder is a specific OSINT technique; this note is the map of the overall landscape before going technique-by-technique.

## 2. Categories of OSINT Sources

| Category | Examples |
|---|---|
| Domain/Infrastructure | WHOIS, DNS records, certificate transparency logs, Shodan/Censys |
| Search Engines | Google, Bing, DuckDuckGo (via advanced operators — see [[Google_Dorking]]) |
| Social Media | LinkedIn, Twitter/X, Facebook, Instagram |
| Code Repositories | GitHub, GitLab — often leaking credentials, internal hostnames, API keys in commit history |
| Documents/Files | PDFs, Office documents publicly posted, which carry metadata (see [[Metadata_Extraction]]) |
| Breach Databases | Have I Been Pwned and similar — checking if organizational emails/credentials appear in known breaches |
| Job Postings | Reveal internal tech stack, tools in use, and organizational structure |
| News/Financial Filings | Mergers, acquisitions, leadership changes, and vendor relationships |

## 3. The OSINT Framework (osintframework.com)

A widely used, community-maintained visual index organizing hundreds of free OSINT tools and resources by category (username search, email search, domain, social media, and more) into a clickable tree. It's less a single tool than a **directory of directories** — the practical starting point when you don't yet know which specific tool fits a given recon need.

## 4. Common Standalone Tools

| Tool | Purpose |
|---|---|
| Maltego | Visual link-analysis platform connecting entities (domains, emails, people, companies) via "transforms" that pull from various OSINT sources |
| theHarvester | Automates email, subdomain, and employee name gathering from multiple public sources at once |
| Recon-ng | A modular reconnaissance framework, similar in spirit to Metasploit but focused on OSINT gathering |
| SpiderFoot | Automated OSINT collection across 200+ data sources with a web UI |

## 5. OSINT as a Discipline Beyond Pentesting

Worth noting: OSINT is also a standalone profession (threat intelligence analysts, investigative journalists, law enforcement) — the skills and tools in this folder aren't pentest-exclusive, which is partly why the tooling ecosystem here is so mature and well-documented compared to more pentest-specific technique folders.

## 6. Interview Questions

1. What defines information as OSINT? → **It comes exclusively from publicly available sources, gathered without hacking or unauthorized access**
2. What is osintframework.com, and how is it different from a single tool like theHarvester? → **A community-maintained visual index/directory of hundreds of OSINT tools organized by category, rather than a tool that performs collection itself**
3. Why would checking a breach database like Have I Been Pwned be relevant during a pentest engagement's recon phase? → **It reveals whether organizational email addresses/credentials have already been exposed in known breaches — directly useful for password-related or phishing-related attack planning later**
4. Name two things a public job posting can reveal that's useful for recon. → **Any two of: internal tech stack, tools/software in use, organizational structure, team names, sometimes internal project codenames**

## 7. Key Points

- OSINT means information from **exclusively public sources** — no unauthorized access involved, regardless of how it's used downstream.
- Source categories span domain/infrastructure, search engines, social media, code repos, documents, breach databases, and job postings.
- **osintframework.com** is a directory of tools by category — the practical starting point when you don't know which specific tool fits a need.
- Maltego, theHarvester, Recon-ng, and SpiderFoot are the most commonly used standalone OSINT automation tools.

---
*Related: [[Passive_vs_Active_Recon]], [[Google_Dorking]], [[Social_Media_OSINT]], [[Email_Harvesting]], [[Metadata_Extraction]]*
