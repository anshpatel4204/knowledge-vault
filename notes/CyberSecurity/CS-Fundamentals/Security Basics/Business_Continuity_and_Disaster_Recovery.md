## 1. Introduction

**Business Continuity Planning (BCP)** and **Disaster Recovery (DR)** ensure an organization can continue operating — or recover quickly — after a disruptive event, whether a cyberattack, natural disaster, hardware failure, or human error. Where [[Incident_Response]] focuses on handling a security incident itself, BCP/DR focuses on **keeping the business running** (or restoring it) despite disruption, security-related or otherwise.

## 2. BCP vs DR — The Key Distinction

| Aspect | Business Continuity (BCP) | Disaster Recovery (DR) |
|---|---|---|
| Scope | Broad — keeping the entire business (people, processes, facilities) functioning | Narrower — specifically restoring IT systems/data/infrastructure |
| Focus | "How do we keep operating during disruption?" | "How do we get technology systems back online?" |
| Relationship | DR is typically considered a **subset** of the broader BCP | Supports BCP by restoring the technical capability BCP depends on |

## 3. Key Metrics — RTO and RPO

| Metric | Definition | Answers |
|---|---|---|
| RTO (Recovery Time Objective) | The maximum acceptable **time** a system/process can be down before causing unacceptable impact | "How fast must we recover?" |
| RPO (Recovery Point Objective) | The maximum acceptable **amount of data loss**, measured in time (how far back backups must reach) | "How much data can we afford to lose?" |

**Worked example:** if backups run every 24 hours (RPO = 24 hours) and it takes 4 hours to restore from backup and resume operations (RTO = 4 hours), a failure at hour 23 could lose nearly a full day of data, and the business would be down for up to 4 hours during restoration — these numbers directly drive backup frequency and infrastructure investment decisions.

```
Disaster occurs
       │
   ◄───┴────────── RPO (max acceptable data loss, backward-looking) 
Last backup                                              Disaster        Systems restored
                                                              │◄──── RTO (max acceptable downtime) ────►│
```

## 4. Other Key Metrics

| Metric | Definition |
|---|---|
| MTD (Maximum Tolerable Downtime) | The absolute longest a business function can be unavailable before causing severe/unrecoverable harm — the outer boundary RTO must stay within |
| WRT (Work Recovery Time) | Time needed after systems are restored to actually resume normal business operations (data verification, catching up on backlog) |
| MTBF (Mean Time Between Failures) | Average time a system operates before failing — a reliability metric informing how often DR capability might actually be needed |
| MTTR (Mean Time To Repair) | Average time to fix a failed component/system |

## 5. Business Impact Analysis (BIA)

The foundational process identifying and prioritizing critical business functions, and quantifying the impact of their disruption over time — directly determines appropriate RTO/RPO targets for each function.

**BIA process:**
1. Identify critical business functions/processes.
2. Determine dependencies (systems, data, personnel, vendors) for each.
3. Assess the impact of disruption over increasing time periods (1 hour, 1 day, 1 week down).
4. Assign RTO/RPO targets based on that impact analysis.
5. Prioritize recovery order — not everything can be restored simultaneously, so critical functions get restored first.

## 6. Backup Strategies

| Type | Description | Trade-off |
|---|---|---|
| Full Backup | Complete copy of all data | Simplest to restore, but slowest/largest to create |
| Incremental Backup | Only data changed since the **last backup of any type** | Fastest to create, but restoration requires the last full backup + every incremental since |
| Differential Backup | Only data changed since the **last full backup** | Faster restore than incremental (only need full + latest differential), but grows larger over time until the next full backup |
| Snapshot | Point-in-time image of a system/volume, often near-instantaneous | Excellent for fast recovery points, but typically not a substitute for offsite/immutable backups |

**3-2-1 Backup Rule:** keep **3** copies of data, on **2** different media types, with **1** copy stored offsite — a widely cited baseline for resilient backup strategy, increasingly extended to "3-2-1-1-0" (adding one immutable/air-gapped copy, and zero errors verified through regular restore testing).

**Why offline/immutable backups matter specifically against ransomware:** if backups are continuously accessible from the production network, ransomware can encrypt or delete them too — offline, air-gapped, or immutable (write-once) backups are the primary defense ensuring recoverability regardless of what happens to production systems.

## 7. DR Site Types

| Site Type | Readiness | Cost | Failover Time |
|---|---|---|---|
| Hot Site | Fully operational, real-time data replication, ready to take over almost immediately | Highest | Minutes |
| Warm Site | Partially configured, some equipment/data present, needs some setup | Moderate | Hours |
| Cold Site | Basic facility/infrastructure only, no pre-installed systems/data | Lowest | Days |
| Cloud/Virtual DR | Infrastructure-as-code and cloud backups enable rapid, elastic recovery without maintaining a dedicated physical site | Variable, often more cost-efficient | Minutes to hours |

## 8. High Availability and Redundancy

Complementary to DR — designing systems to **avoid** downtime in the first place rather than just recovering from it quickly afterward: redundant power/network paths, clustering, load balancing, geographically distributed infrastructure. High availability reduces how often DR procedures are actually needed.

## 9. Testing BCP/DR Plans

An untested plan is unreliable by default — regular testing validates that RTO/RPO targets are actually achievable in practice.

| Test Type | Description |
|---|---|
| Tabletop/Walkthrough | Discussion-based review of the plan, no actual systems touched (see [[Incident_Response]]) |
| Simulation | A realistic scenario is role-played, testing decision-making without full technical failover |
| Parallel Test | The DR site/systems are activated alongside production (not replacing it), verifying they work without risking live operations |
| Full Interruption Test | Production is actually taken offline and failed over to DR — the most realistic test, but also the highest-risk, typically reserved for mature programs |

## 10. Interview Questions

1. What's the difference between BCP and DR? → **BCP is the broader plan for keeping the whole business operating during disruption; DR is the narrower, IT-focused subset restoring technical systems/data**
2. What's the difference between RTO and RPO? → **RTO is the maximum acceptable downtime before recovery; RPO is the maximum acceptable data loss, measured backward in time from the disaster**
3. What does a Business Impact Analysis (BIA) determine? → **The criticality of business functions and the appropriate RTO/RPO targets based on quantified impact of disruption over time**
4. Why are offline/immutable backups specifically important against ransomware? → **Continuously-accessible backups on the production network can be encrypted or deleted by the same ransomware attacking production systems**
5. What's the difference between a hot site and a cold site? → **A hot site is fully operational and ready for near-immediate failover; a cold site is a bare facility requiring significant setup before use**
6. Why is a full interruption test considered the most realistic but also highest-risk form of DR testing? → **It actually takes production offline and fails over to DR, validating real-world readiness but risking live operations if something goes wrong**

## 11. Key Points

- **BCP** keeps the overall business running; **DR** (a subset of BCP) specifically restores IT systems/data.
- **RTO** (downtime tolerance) and **RPO** (data loss tolerance) are the two defining metrics, derived from a **Business Impact Analysis**.
- The **3-2-1 backup rule** (3 copies, 2 media types, 1 offsite) — extended with immutable/offline copies — is essential, especially against ransomware.
- DR site options range from **cold** (cheap, slow) to **hot** (expensive, near-instant) to increasingly common **cloud-based** DR.
- Plans must be **regularly tested** (tabletop through full interruption) — an untested plan's RTO/RPO targets are unverified assumptions.

---
*Related: [[Incident_Response]], [[Risk_Management]], [[Data_Security_and_Classification]]*
