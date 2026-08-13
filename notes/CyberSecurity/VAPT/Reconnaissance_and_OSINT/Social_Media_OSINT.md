## 1. Introduction

Technical infrastructure isn't the only attack surface — people are, and social media is the richest public source of information about an organization's employees, structure, and culture. This note covers gathering that intelligence responsibly and legally, directly feeding into social-engineering-style testing scenarios referenced in [[Legal_and_Ethical_Considerations]].

## 2. What Social Media Reveals

| Platform | Typical Intelligence Value |
|---|---|
| LinkedIn | Organizational structure, job titles, reporting lines, technology stack mentioned in profiles/job history, recent hires (often a soft-target for pretexting) |
| Twitter/X | Real-time company announcements, employee complaints/venting that can reveal internal tooling or friction points |
| Facebook/Instagram | Personal details useful for password-guessing or targeted pretexting (pet names, birthdates, locations) |
| GitHub | Employee-linked repositories, sometimes leaking internal hostnames, code snippets, or credentials in commit history |
| Glassdoor/Indeed reviews | Internal culture, tooling, and occasionally security process details disclosed by current/former employees |

## 3. Building an Organizational Chart from Public Data

A common recon deliverable is an inferred org chart — cross-referencing LinkedIn titles and connections to map reporting structure, IT/security team members (useful for knowing who to avoid or who to specifically test in social engineering scenarios per the RoE), and recent joiners (often less security-aware and a common phishing target).

## 4. Tools

| Tool | Purpose |
|---|---|
| Sherlock | Searches for a given username across many platforms simultaneously |
| theHarvester | Also pulls employee names/emails from social sources alongside its domain recon function |
| Maltego | Visual link-analysis connecting people, companies, and social profiles via transforms |
| LinkedIn Sales Navigator (or manual search) | Structured organizational search, filterable by company/title |

## 5. Ethical Boundary Specific to This Technique

Social media OSINT sits closer to the ethical edge than domain/infrastructure recon — it involves real individuals' personal information. Professional practice restricts this activity to what's directly relevant to the authorized engagement (e.g., building a phishing simulation target list because social engineering is explicitly in scope) rather than open-ended personal investigation, and any personal data collected should be handled per the data handling rules in the RoE (see [[Scoping_and_Rules_of_Engagement]]).

## 6. Interview Questions

1. Why is social media OSINT considered closer to an ethical edge case than domain/DNS recon? → **It involves gathering personal information about real, named individuals rather than purely technical infrastructure data, requiring tighter scope discipline**
2. What kind of intelligence does LinkedIn typically reveal that's directly useful for a pentest? → **Organizational structure, job titles/reporting lines, technology stack mentioned in profiles, and recent hires who are often less security-aware**
3. Why are recently hired employees often a specific focus in social-engineering-oriented recon? → **New employees are typically less familiar with internal security processes and more likely to comply with an unfamiliar but plausible-seeming request, making them a common phishing/pretexting target**
4. What governs how personal data collected via social media OSINT should be stored and eventually disposed of during an engagement? → **The data handling rules agreed upon during scoping, documented in the Rules of Engagement**

## 7. Key Points

- Social media is the richest public source of intelligence about an organization's **people**, not just its infrastructure.
- LinkedIn, Twitter/X, GitHub, and review sites like Glassdoor each reveal different intelligence — organizational structure, tooling, and internal culture/friction points.
- Building an inferred **organizational chart** from public data is a common recon deliverable, especially useful for scoping social engineering scenarios.
- This technique carries tighter **ethical constraints** than infrastructure recon since it involves real individuals — scope discipline and RoE data handling rules matter more here.

---
*Related: [[Legal_and_Ethical_Considerations]], [[OSINT_Framework_and_Tools]], [[Email_Harvesting]], [[Social_Engineering]] (CS-Fundamentals/Security Basics)*
