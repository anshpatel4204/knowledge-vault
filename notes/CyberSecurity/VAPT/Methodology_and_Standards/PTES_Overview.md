## 1. Introduction

The **Penetration Testing Execution Standard (PTES)** is one of the most widely referenced frameworks for structuring a penetration test end to end. It doesn't prescribe specific tools — it defines the **phases** a professional engagement should move through, so results are consistent and comparable regardless of who performs the test. It's the natural starting point for this folder because nearly every other standard and every note in the rest of VAPT maps back onto one of its seven phases.

## 2. The Seven PTES Phases

```
1. Pre-engagement Interactions — scoping, contracts, Rules of Engagement
2. Intelligence Gathering — recon (see Folder 2: Reconnaissance_and_OSINT)
3. Threat Modeling — identifying likely attack vectors against this specific target
4. Vulnerability Analysis — mapping discovered weaknesses to potential exploits
5. Exploitation — gaining access, bypassing defenses
6. Post-Exploitation — privilege escalation, lateral movement, data value assessment
7. Reporting — documenting findings, risk, and remediation
```

| Phase | Answers the Question |
|---|---|
| Pre-engagement | What are we allowed to do, and to what? |
| Intelligence Gathering | What does this target actually look like from the outside/inside? |
| Threat Modeling | Given what we know, how would a real attacker most likely come in? |
| Vulnerability Analysis | Which of the discovered weaknesses are actually exploitable? |
| Exploitation | Can we actually get in? |
| Post-Exploitation | Once in, what's the real business impact? |
| Reporting | How do we communicate this so it actually gets fixed? |

## 3. Why PTES Matters Beyond the Checklist

PTES also defines **technical guidelines** underneath each phase — recommended tools, minimum depth of testing, and how to document evidence — which is why it's often cited as a baseline for scoping conversations with clients ("we're following PTES" sets a shared expectation of thoroughness). It deliberately does not compete with OWASP or NIST SP 800-115 — see [[OWASP_Testing_Guide]] and [[NIST_SP_800-115]] — but sits at a more general, engagement-wide level that those standards can slot into during the Vulnerability Analysis and Exploitation phases.

## 4. How This Maps to the Rest of the VAPT Folder

| PTES Phase | Where It Lives in This Vault |
|---|---|
| Intelligence Gathering | Folder 2 — Reconnaissance_and_OSINT |
| Vulnerability Analysis / Exploitation | Folders 3-9 (Network, Web, API, AD, Mobile, Wireless, Cloud) |
| Post-Exploitation | Folder 10 — Post_Exploitation_and_Privilege_Escalation |
| Reporting | Folder 13 — Reporting_and_Risk_Scoring |

## 5. Interview Questions

1. What are the seven phases of PTES, in order? → **Pre-engagement, Intelligence Gathering, Threat Modeling, Vulnerability Analysis, Exploitation, Post-Exploitation, Reporting**
2. What does PTES define that raw tool checklists don't? → **A structured, repeatable methodology and technical guidelines per phase, so engagements are consistent regardless of tester**
3. Where does Threat Modeling sit in the PTES lifecycle, and why? → **After Intelligence Gathering, before Vulnerability Analysis — you need recon data first to model realistic attack vectors against the specific target**
4. Does PTES replace OWASP or NIST SP 800-115? → **No — PTES governs the overall engagement lifecycle; OWASP and NIST provide deeper technical guidance that slots into PTES's Vulnerability Analysis/Exploitation phases**

## 6. Key Points

- PTES defines **seven phases**, not specific tools — pre-engagement, intelligence gathering, threat modeling, vulnerability analysis, exploitation, post-exploitation, reporting.
- It's an **engagement-wide** framework — other standards (OWASP, NIST SP 800-115) provide deeper technical detail within its phases rather than competing with it.
- Citing "PTES-aligned" in a proposal sets a shared, professional expectation of thoroughness with a client.
- Every later folder in this vault's VAPT track maps back onto one or more PTES phases.

---
*Related: [[OSSTMM_Overview]], [[OWASP_Testing_Guide]], [[NIST_SP_800-115]], [[VAPT_Engagement_Lifecycle]], [[Security_Testing_Types]]*
