# Azure Disaster Recovery

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
The set of patterns and Azure services (Azure Site Recovery, geo-redundant storage, multi-region deployment, Traffic Manager/Front Door failover) used to meet a workload's RTO/RPO targets under regional or major service failure.

## Why Architects Use It
DR design is a direct translation of business risk tolerance into technical architecture — getting it wrong either wastes money (over-engineering for an RTO nobody needs) or leaves the business exposed (under-engineering for a real compliance/continuity requirement).

## Architecture Considerations
- **Start from RTO/RPO, not from technology** — the business requirement should determine the pattern, not the reverse.
- **Active-passive**: secondary region on standby, promoted on failure — lower cost, higher RTO.
- **Active-active**: both regions live, traffic distributed — near-zero RTO, significantly higher cost and complexity (data consistency across regions becomes the hard problem).
- **Backup vs. replication**: backup protects against logical/data corruption and ransomware (point-in-time restore); replication protects against infrastructure/regional failure — most serious DR strategies need both.
- **DR testing**: an untested DR plan is not a DR plan — regular failover drills are part of the architecture, not an afterthought.

## Common Mistakes
- Designing active-active for workloads whose actual RTO tolerance would be fine with active-passive, at multiples of the cost.
- Relying solely on geo-redundant storage replication as "DR" without an actual application-level failover plan.
- Never testing failover, discovering gaps only during a real incident.

## Principal-Level Questions
- How would you design DR for a business-critical application with an RTO of 30 minutes and RPO of 5 minutes?
- How do you handle data consistency in an active-active multi-region design?

## Scenario Question
> Design DR for a business-critical application with an RTO of 30 minutes and RPO of 5 minutes.

## Strong Answer (Outline)
1. RPO of 5 minutes rules out daily backups as the sole mechanism — requires near-continuous replication (e.g., geo-replicated database with a low replication lag, or transaction log shipping).
2. RTO of 30 minutes is achievable with a **warm standby (active-passive with pre-provisioned but scaled-down secondary)** rather than requiring full active-active — active-active would meet it too but at unnecessary cost/complexity for a 30-minute target.
3. Automate failover (Traffic Manager/Front Door health-probe-triggered, or scripted runbook) rather than a fully manual process, since 30 minutes leaves little room for manual coordination.
4. Regular failover drills (quarterly, at minimum) to validate the RTO/RPO is actually achievable, not just theoretical.
5. Layer in backup (point-in-time restore) for the corruption/ransomware case that replication alone doesn't solve — replication would faithfully copy corrupted data too.

## Follow-Up Questions
- How would you validate RPO is actually being met in production, not just assumed?
- What changes if the RTO requirement drops to 5 minutes?

## Trade-offs
- Active-active: best RTO, highest cost/complexity, hardest data-consistency problem.
- Warm standby: good RTO, moderate cost, simpler operational model.
- Backup-only: cheapest, but RTO/RPO are typically hours, not minutes.
