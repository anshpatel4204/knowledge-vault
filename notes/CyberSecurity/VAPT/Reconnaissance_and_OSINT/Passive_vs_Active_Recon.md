## 1. Introduction

Reconnaissance is the first hands-on phase of a real engagement — PTES calls it "Intelligence Gathering," NIST SP 800-115 folds it into "Discovery" (see [[PTES_Overview]], [[NIST_SP_800-115]]). Before learning individual techniques, every recon activity needs to be classified along one critical axis: does it touch the target at all, or not?

## 2. The Core Distinction

| Type | Definition | Detectable by Target? |
|---|---|---|
| Passive Recon | Gathering information without directly interacting with the target's systems — using third-party sources instead | No — invisible to the target |
| Active Recon | Directly interacting with the target's systems (sending packets, requests, queries to their infrastructure) | Yes — potentially logged, alerted on, or blocked |

## 3. Examples of Each

| Passive | Active |
|---|---|
| WHOIS lookups via a third-party registrar database | Port scanning the target's IP ranges directly |
| Search engine queries (Google Dorking) | Sending DNS queries directly to the target's own nameservers |
| Reviewing social media / LinkedIn profiles | Visiting the target's website and probing its behavior |
| Shodan/Censys internet-wide scan databases | Banner grabbing by connecting directly to a service |
| Reviewing public financial filings, press releases, job postings | Attempting to enumerate valid usernames via a login form |

## 4. Why the Distinction Matters for Scoping and Stealth

This isn't just an academic classification — it directly affects two practical decisions covered in Folder 1:

| Consideration | Why It Matters |
|---|---|
| Scope/RoE | Some engagements (especially Red Team-style, see [[Security_Testing_Types]]) restrict early recon to passive-only to maximize stealth before the "reveal" of active testing |
| Detection risk | Active recon can trigger IDS/IPS alerts or show up in logs — a black box test's realism sometimes depends on staying passive as long as possible |
| Legal boundary | Passive recon using only public, third-party sources rarely raises authorization questions; active recon against the target's own infrastructure is unambiguously covered by the RoE's scope (see [[Scoping_and_Rules_of_Engagement]]) |

## 5. The Practical Order of Operations

```
1. Exhaust passive sources first — WHOIS, DNS history, Shodan/Censys, search engines, social media
2. Build as complete a target profile as possible with zero footprint on the target
3. Only then move to active techniques — direct scanning, enumeration, probing
   (this maximizes information gathered per unit of detection risk taken)
```

## 6. Interview Questions

1. What's the core difference between passive and active reconnaissance? → **Passive recon gathers information via third-party sources without touching the target's systems; active recon directly interacts with the target's own infrastructure**
2. Give one example each of a passive and an active recon technique. → **Passive: WHOIS lookup via a third-party registrar database, or a Shodan search; Active: port scanning the target's IPs directly, or banner grabbing by connecting to a service**
3. Why do some Red Team engagements deliberately restrict early recon to passive-only techniques? → **To maximize stealth and avoid tipping off the target's Blue Team before active testing begins, better simulating a real stealthy adversary**
4. Why is passive recon generally lower legal/scope risk than active recon? → **It relies on public third-party sources rather than directly interacting with the target's own systems, which sidesteps most authorization ambiguity that active techniques carry**

## 7. Key Points

- **Passive recon** uses third-party sources and never touches the target directly — invisible and generally lower-risk.
- **Active recon** directly interacts with the target's own systems — more information yield, but detectable and requires clear RoE authorization.
- Best practice is to **exhaust passive sources first**, then move to active techniques only once a solid baseline profile exists.
- This distinction directly shapes scoping decisions and stealth requirements, especially in Red Team-style engagements.

---
*Related: [[PTES_Overview]], [[Scoping_and_Rules_of_Engagement]], [[OSINT_Framework_and_Tools]], [[Network_Footprinting]]*
