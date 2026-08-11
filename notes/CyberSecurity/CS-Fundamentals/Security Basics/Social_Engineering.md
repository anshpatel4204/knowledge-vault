## 1. Introduction

**Social engineering** is the manipulation of human psychology — trust, fear, urgency, curiosity, authority — to trick people into divulging confidential information or performing actions that compromise security. It bypasses technical controls entirely by targeting the human element, which is consistently the weakest link in any security program regardless of how strong the technology is.

## 2. Why Social Engineering Works

Social engineering exploits well-documented cognitive and social tendencies:

| Principle | How It's Exploited |
|---|---|
| Authority | Impersonating an executive, IT admin, or law enforcement to demand compliance |
| Urgency/Scarcity | Creating time pressure ("your account will be locked in 10 minutes") to short-circuit careful thinking |
| Fear | Threatening consequences (legal action, job loss, account suspension) |
| Trust/Liking | Building rapport or impersonating a known, trusted contact |
| Reciprocity | Offering something first to create a sense of obligation |
| Social Proof | "Everyone else already did this" pressure |

## 3. Phishing (Email-Based)

The most common social engineering vector — fraudulent emails designed to trick recipients into revealing credentials, clicking malicious links, or opening infected attachments.

| Variant | Description |
|---|---|
| Phishing | Mass, untargeted emails sent broadly, hoping a percentage of recipients fall for it |
| Spear Phishing | Targeted at a specific individual/organization, using personalized details for credibility |
| Whaling | Spear phishing targeted specifically at senior executives ("big fish") |
| Clone Phishing | Duplicates a legitimate previously-sent email, replacing links/attachments with malicious ones |
| Business Email Compromise (BEC) | Impersonates an executive or vendor to trick finance/staff into wire transfers or sensitive data disclosure — among the most financially damaging attack categories overall |

## 4. Phishing Delivered via Other Channels

| Variant | Channel |
|---|---|
| Vishing | Voice phishing — phone calls impersonating IT support, banks, or executives |
| Smishing | SMS/text message phishing |
| Quishing | QR-code-based phishing (embedding malicious URLs in scannable codes) |

## 5. Physical/In-Person Social Engineering

| Technique | Description |
|---|---|
| Pretexting | Fabricating a believable scenario/identity to extract information (e.g., posing as an auditor or new employee) |
| Tailgating (Piggybacking) | Following an authorized person through a secured door without their own access credential, often relying on politeness |
| Baiting | Leaving an infected physical device (e.g., a USB drive labeled "Payroll 2024") for a victim to find and plug in out of curiosity |
| Quid Pro Quo | Offering a service/benefit (e.g., fake "IT support") in exchange for information or access |
| Shoulder Surfing | Directly observing someone entering credentials or viewing sensitive information |
| Dumpster Diving | Searching discarded physical trash for sensitive documents, credentials, or hardware |

## 6. Pretexting in Detail

Involves an attacker inventing a fabricated scenario and often a false identity to build enough legitimacy to extract information or access — frequently combined with impersonation (a vendor, auditor, new hire, or IT support) and used as a setup for other techniques (vishing calls often rely heavily on a well-built pretext).

## 7. Recognizing Phishing — Practical Indicators

- Mismatched or suspicious sender domains (subtle misspellings — "micosoft.com").
- Generic greetings instead of personalization ("Dear Customer").
- Urgency/threat-based language pushing immediate action.
- Requests for credentials, payment, or sensitive data via email/link rather than established channels.
- Hovering over links reveals a destination different from the displayed text.
- Unexpected attachments, especially with executable or macro-enabled formats.
- Poor grammar/spelling (though modern, well-resourced campaigns increasingly avoid this tell).

## 8. Social Engineering Attack Lifecycle

1. **Information Gathering** — reconnaissance via social media, company websites, public records, or previous breaches (OSINT).
2. **Building a Pretext/Relationship** — establishing credibility or rapport with the target.
3. **Exploitation** — executing the actual manipulation (the phishing email, the phone call, the physical approach).
4. **Execution** — the victim performs the desired action (clicks, discloses, grants access).
5. **Exit** — the attacker withdraws, ideally without raising suspicion, sometimes covering tracks.

## 9. Real-World Impact

Social engineering is consistently cited as the initial access vector in the **majority** of reported breaches — because it's often far cheaper and more reliable than developing a technical exploit. High-profile incidents (Twitter's 2020 celebrity account hack, numerous ransomware campaigns, and countless BEC fraud cases) trace back to social engineering rather than sophisticated zero-day exploitation.

## 10. Defenses Against Social Engineering

| Defense | Description |
|---|---|
| Security awareness training | Regular, realistic training including simulated phishing campaigns (see [[Security_Awareness_and_Human_Factor]]) |
| Verification procedures | Out-of-band confirmation for sensitive requests (e.g., calling a known number to verify a wire transfer request, never using contact info provided in the suspicious message itself) |
| Email security controls | SPF/DKIM/DMARC, spam/phishing filtering, attachment sandboxing |
| MFA | Limits the damage of a successfully phished password alone (see [[Identity_and_Access_Management]]) |
| Physical access controls | Badge readers, mantraps, visitor escort policies (see [[Physical_Security]]) |
| Clear reporting channels | Easy, blame-free process for employees to report suspected social engineering attempts |
| Least privilege | Limits what a successfully socially-engineered account can actually access/do |

## 11. Interview Questions

1. What's the difference between phishing, spear phishing, and whaling? → **Phishing is mass/untargeted; spear phishing targets a specific individual/org with personalization; whaling specifically targets senior executives**
2. What is pretexting? → **Fabricating a believable scenario or false identity to extract information or gain access**
3. What is tailgating, and how is it typically defended against? → **Following an authorized person through a secured door without your own credential; defended with mantraps, badge enforcement, and challenging unfamiliar individuals**
4. What is Business Email Compromise (BEC)? → **Impersonating an executive or vendor to trick staff into fraudulent wire transfers or sensitive disclosures**
5. Why is out-of-band verification recommended for sensitive requests like wire transfers? → **It confirms legitimacy through a separate, trusted channel rather than the potentially compromised channel the request arrived through**
6. Why does social engineering remain effective despite widespread awareness training? → **It exploits fundamental human psychology (authority, urgency, trust) rather than technical weaknesses, and attackers continuously adapt their pretexts**

## 12. Key Points

- Social engineering exploits **human psychology**, not technical vulnerabilities — bypassing even strong technical controls.
- **Phishing** (and its variants: spear phishing, whaling, vishing, smishing) is the dominant delivery mechanism, especially **BEC** for financial impact.
- Physical/in-person techniques (**pretexting, tailgating, baiting, shoulder surfing**) remain effective and are often underestimated.
- Defenses combine **awareness training, verification procedures, MFA, and least privilege** — no single control fully solves the human factor.
- Social engineering is the **initial access vector in the majority of real-world breaches**, making it arguably the highest-leverage area for security investment.

---
*Related: [[Threat_Actors_and_Hackers]], [[Malware_Types]], [[Security_Awareness_and_Human_Factor]], [[Identity_and_Access_Management]]*
