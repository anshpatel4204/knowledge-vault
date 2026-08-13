## 1. Introduction

**Email harvesting** builds a validated list of an organization's email addresses — the foundation for phishing simulation campaigns (when in scope) and a strong signal for the organization's email address naming convention, which itself becomes useful for later credential-related testing (e.g., predicting valid usernames for password spraying in Folder 3 or Folder 6).

## 2. Sources for Harvesting

| Source | What It Provides |
|---|---|
| Search engines / Google Dorking | `"@example.com"` searches surface publicly posted addresses (see [[Google_Dorking]]) |
| Company website | Staff directories, "About Us"/"Team" pages, press contact addresses |
| Social media / LinkedIn | Employee names, combined with a discovered naming convention to predict addresses |
| Breach databases (Have I Been Pwned, etc.) | Confirms an address is real/active and may reveal it was part of a prior breach — additional context for risk framing |
| Automation tools (theHarvester, Hunter.io) | Aggregate multiple sources automatically in one pass |

## 3. Inferring the Naming Convention

Once even a handful of confirmed employee emails are found, the underlying pattern is usually obvious and can be applied to every other known employee name (from LinkedIn, per [[Social_Media_OSINT]]) to generate a much larger predicted list:

```
Common patterns:
first.last@example.com
firstlast@example.com
flast@example.com
first@example.com
```

Predicted addresses should be validated (e.g., via SMTP verification techniques, or simply observing which predicted addresses show up independently elsewhere) before being treated as confirmed — an unvalidated guess isn't a reliable finding.

## 4. Where This Feeds Downstream

| Use Case | How |
|---|---|
| Phishing simulation | Target list for a social engineering test (must be explicitly in scope — see [[Scoping_and_Rules_of_Engagement]]) |
| Password spraying | A predicted username list to pair with common/leaked passwords during authenticated testing |
| Credential stuffing risk assessment | Checking harvested addresses against known breach dumps to gauge organizational exposure to reused-password attacks |

## 5. Interview Questions

1. Why is discovering an organization's email naming convention valuable beyond just having a list of addresses? → **It allows predicting valid email addresses/usernames for every other known employee name, dramatically expanding the useful target list from just a few confirmed samples**
2. Name two sources for harvesting employee email addresses. → **Any two of: search engines/Google Dorking, the company website's staff/team pages, LinkedIn combined with a known naming pattern, breach databases, automation tools like theHarvester**
3. Why should a predicted email address be validated before being used as a confirmed finding? → **A predicted address based on a guessed naming pattern may not actually exist — treating an unvalidated guess as confirmed risks inaccurate findings or wasted phishing simulation targets**
4. How does email harvesting connect to password spraying later in an engagement? → **Harvested/predicted email addresses double as a likely username list, which can be paired with common or leaked passwords during authenticated network/AD testing**

## 6. Key Points

- Email harvesting combines direct discovery (search engines, websites, breach databases) with **naming-convention inference** to build a much larger predicted address list.
- Predicted addresses should be **validated**, not treated as confirmed findings on pattern-matching alone.
- Feeds directly into **phishing simulation** target lists (only if in scope) and **password spraying** username lists later in the engagement.
- Breach database cross-referencing adds risk context — revealing whether harvested addresses have prior exposure history.

---
*Related: [[Google_Dorking]], [[Social_Media_OSINT]], [[OSINT_Framework_and_Tools]], [[Scoping_and_Rules_of_Engagement]]*
