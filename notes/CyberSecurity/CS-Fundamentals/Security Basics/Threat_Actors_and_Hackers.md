## 1. Introduction

A **threat actor** is any individual or group that carries out (or has the potential to carry out) an action that could cause harm to a system, network, or organization. Understanding who's behind an attack — their motivation, skill level, and resources — directly shapes how an organization prioritizes defenses. Not all attackers are equal, and treating them as such leads to misallocated security effort.

## 2. Hacker Categories by Intent ("Hat" Classification)

| Type | Description |
|---|---|
| White Hat | Ethical hackers — authorized to test systems (pentesters, bug bounty hunters), report findings responsibly |
| Black Hat | Malicious hackers — attack systems without authorization for personal gain, damage, or ideology |
| Gray Hat | Operates without authorization but without malicious intent — may find and disclose a vulnerability without permission, occupying legally ambiguous territory |
| Blue Hat | Externally invited to test systems before launch (e.g., Microsoft's BlueHat events), or sometimes used to describe a vengeful ex-employee (older/less common usage) |
| Red Hat | Aggressively counter-attacks black hats (vigilante-style) — rare, controversial, often crosses into illegal territory itself |

## 3. Threat Actors by Skill and Resources

| Actor Type | Skill Level | Resources | Motivation |
|---|---|---|---|
| Script Kiddie | Low — uses pre-built tools/scripts without deep understanding | Minimal | Thrill, reputation, curiosity |
| Hacktivist | Varies | Moderate, often crowd-sourced | Political/social ideology (e.g., Anonymous-style campaigns) |
| Cybercriminal | Moderate to high | Moderate — often organized, profit-driven | Financial gain (ransomware, fraud, data theft for sale) |
| Insider Threat | Varies (has legitimate access already) | Access-based, not skill-based | Revenge, financial gain, coercion, or unintentional negligence |
| APT (Advanced Persistent Threat) | Very high | Extensive — often nation-state funded | Espionage, long-term strategic access, sabotage |
| Nation-State Actor | Very high | Extensive — government-backed | Espionage, warfare, critical infrastructure disruption, geopolitical advantage |

## 4. Advanced Persistent Threats (APT) — Deep Dive

An **APT** is a sophisticated, well-resourced threat actor (frequently nation-state-affiliated) that gains unauthorized access and remains **undetected for an extended period**, prioritizing stealth and persistence over speed.

**Characteristics:**
- Highly targeted (specific organizations/sectors, not opportunistic).
- Uses custom malware and zero-day exploits alongside standard tools.
- Multi-stage operations: initial access → persistence → lateral movement → data exfiltration, often over months or years.
- Frequently tracked by security researchers under naming conventions like "APT28," "APT29," or vendor-specific names (e.g., "Fancy Bear," "Cozy Bear").

## 5. Insider Threats

A threat originating from someone with **legitimate authorized access** — employees, contractors, or business partners — making it particularly dangerous since it bypasses perimeter defenses entirely.

| Type | Description |
|---|---|
| Malicious Insider | Deliberately misuses access (theft, sabotage, revenge) |
| Negligent Insider | Unintentionally causes harm (falls for phishing, misconfigures a system, loses a device) |
| Compromised Insider | Legitimate credentials stolen/used by an external attacker, making the activity appear to originate internally |

**Why insiders are hard to detect:** their activity often looks like normal authorized behavior, making it far less likely to trigger perimeter-focused security controls — this is a major driver behind modern **User and Entity Behavior Analytics (UEBA)** and Zero Trust approaches (see [[Defense_in_Depth_and_Zero_Trust]]).

## 6. Hacktivists

Motivated by political, social, or ideological causes rather than financial gain — common tactics include website defacement, DDoS attacks against targeted organizations, and leaking sensitive data to embarrass or pressure a target. Notable example: the loosely organized collective **Anonymous**.

## 7. Cybercriminal Organizations

Increasingly operate like businesses — **Ransomware-as-a-Service (RaaS)** groups develop and lease ransomware tools to affiliates for a cut of the profits, division-of-labor models (initial access brokers selling footholds to other criminal groups), and dark web marketplaces trading stolen data/credentials/exploits.

## 8. Threat Actor Motivations Summary

| Motivation | Typical Actor |
|---|---|
| Financial gain | Cybercriminals, ransomware groups |
| Espionage / strategic advantage | Nation-states, APTs |
| Ideology / political statement | Hacktivists |
| Reputation / thrill | Script kiddies |
| Revenge / personal grievance | Malicious insiders |
| Curiosity / research (authorized) | White hat researchers, bug bounty hunters |

## 9. Why This Classification Matters for Defense

Different threat actors warrant different defensive priorities:

| If facing... | Prioritize |
|---|---|
| Script kiddies / opportunistic scanning | Basic hygiene — patching, firewalls, strong default configs |
| Cybercriminals / ransomware | Backups, endpoint detection, email/phishing defenses, network segmentation |
| APTs / nation-states | Advanced detection (SIEM/SOC), threat hunting, Zero Trust, supply chain security |
| Insider threats | Least privilege, DLP, behavioral monitoring, offboarding processes |

## 10. Interview Questions

1. What's the difference between a white hat and a gray hat hacker? → **White hat operates with authorization; gray hat operates without authorization but without malicious intent**
2. What defines an APT? → **A sophisticated, well-resourced actor prioritizing long-term, stealthy, targeted access — often nation-state affiliated**
3. Why are insider threats considered particularly dangerous? → **They already have legitimate access, bypassing perimeter defenses, and their activity can look like normal authorized behavior**
4. What's the primary motivation of a hacktivist compared to a cybercriminal? → **Hacktivists are ideologically/politically motivated; cybercriminals are financially motivated**
5. What is Ransomware-as-a-Service (RaaS)? → **A business model where ransomware developers lease their tools to affiliates in exchange for a cut of ransom profits**
6. Name the three types of insider threats. → **Malicious, negligent, and compromised**

## 11. Key Points

- Threat actors range from low-skill **script kiddies** to highly resourced **APTs/nation-states** — skill and resources should shape defensive priorities.
- **Insider threats** are especially dangerous because they start with legitimate access, bypassing perimeter-focused controls.
- **Hacktivists** are ideologically driven; **cybercriminals** are financially driven, increasingly operating with business-like structures (RaaS).
- **APTs** prioritize stealth and persistence over speed, often operating undetected for months or years.
- Matching defenses to the realistic threat actors an organization actually faces is more effective than generic, undifferentiated security spending.

---
*Related: [[Malware_Types]], [[Social_Engineering]], [[Cyber_Kill_Chain_and_MITRE_ATTACK]]*
