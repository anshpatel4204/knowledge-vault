## 1. Introduction

This note ties together every stage of a real-world VAPT engagement into one end-to-end lifecycle — synthesizing PTES's phases, NIST SP 800-115's four-phase process, and the practical scoping/legal work from earlier in this folder into the sequence an actual project follows from first client contact to final sign-off.

## 2. The Full Lifecycle

```
1. Pre-Sales / Initial Contact   — client requirement gathering, high-level proposal
2. Scoping                       — RoE, in/out-of-scope assets, testing type (see [[Scoping_and_Rules_of_Engagement]])
3. Kickoff Meeting                — confirm scope, contacts, timeline with the client
4. Reconnaissance                 — passive/active info gathering (Folder 2)
5. Scanning & Enumeration         — identifying live hosts, services, potential entry points
6. Vulnerability Analysis         — mapping findings to known/potential exploits
7. Exploitation                   — attempting to gain access, within agreed RoE boundaries
8. Post-Exploitation               — privilege escalation, lateral movement, impact assessment (Folder 10)
9. Reporting                      — findings, CVSS scoring, remediation guidance (Folder 13)
10. Debrief / Presentation         — walking the client through results, answering questions
11. Remediation Support (optional) — clarifying fixes, answering developer/ops questions
12. Retesting                      — verifying fixes actually closed the reported findings
13. Closure                        — final sign-off, secure destruction of any sensitive data collected
```

## 3. Where Each Standard Maps In

| Lifecycle Stage | Standard Alignment |
|---|---|
| Scoping | RoE principles from all standards; explicitly required by PTES's Pre-engagement phase |
| Recon → Vulnerability Analysis | PTES Intelligence Gathering/Threat Modeling/Vulnerability Analysis; NIST's Discovery phase |
| Exploitation → Post-Exploitation | PTES Exploitation/Post-Exploitation; NIST's Attack phase |
| Reporting | All standards converge here — see Folder 13 |

## 4. Why Retesting Is a Distinct, Often-Skipped Stage

Many organizations treat the report as the end of the engagement, but a finding marked "fixed" by the client team isn't actually verified until a tester confirms it. Retesting closes the loop the same way [[Vulnerability_Management]]'s "Verify" stage does in the broader vulnerability management lifecycle — skipping it means the organization is trusting an unverified claim of remediation.

## 5. Interview Questions

1. List the lifecycle stages between Scoping and Reporting, in order. → **Kickoff Meeting → Reconnaissance → Scanning & Enumeration → Vulnerability Analysis → Exploitation → Post-Exploitation**
2. Why is Retesting a distinct stage rather than assumed once a report is delivered? → **A finding marked "fixed" by the client isn't actually verified until a tester independently confirms the fix — skipping retesting means trusting an unverified claim**
3. Which stage of this lifecycle corresponds to PTES's "Pre-engagement Interactions" phase? → **Scoping (and the Kickoff Meeting that follows it)**
4. What should happen to sensitive data collected during testing once the engagement closes? → **Secure destruction, as agreed in the data handling rules defined during scoping**

## 6. Key Points

- A full VAPT engagement runs from **pre-sales through closure** — testing itself (recon → exploitation → post-exploitation) is only the middle portion.
- Every step maps back onto PTES/NIST SP 800-115 phases already covered earlier in this folder — this note is the synthesized, practical version.
- **Retesting** is a distinct, essential stage that verifies remediation rather than trusting it — frequently skipped by less mature programs.
- Data handling and secure destruction obligations, agreed during scoping, extend through **Closure**, not just the active testing window.

---
*Related: [[PTES_Overview]], [[NIST_SP_800-115]], [[Scoping_and_Rules_of_Engagement]], [[Vulnerability_Management]], [[Reporting_and_Risk_Scoring]] (Folder 13)*
