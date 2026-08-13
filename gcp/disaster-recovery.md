# GCP Disaster Recovery

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
GCP's DR building blocks: multi-region storage/database replication (e.g., Spanner's native multi-region strong consistency, Cloud SQL cross-region replicas), Cloud Load Balancing for global failover, and backup services.

## Why Architects Use It
Same principle as Azure DR — translate business RTO/RPO into an architecture — with Cloud Spanner notable as a distinctive option for near-zero RPO multi-region consistency without the typical eventual-consistency trade-off.

## Architecture Considerations
- **Start from RTO/RPO**, same discipline as [`azure/disaster-recovery.md`](../azure/disaster-recovery.md).
- **Cloud Spanner** offers synchronous multi-region replication with strong consistency — a strong option when near-zero RPO and multi-region writes are both required, at a higher cost/complexity than typical regional databases.
- **Cloud SQL cross-region replicas**: asynchronous, so RPO is non-zero (replication lag) — suitable for many active-passive designs, not for zero-data-loss requirements.
- **Global Load Balancing**: GCP's anycast global LB can direct traffic to healthy regions automatically, simplifying failover compared to manually orchestrated DNS failover.

## Common Mistakes
- Choosing Cloud Spanner by default for its consistency guarantees without needing multi-region writes, paying for capability the workload doesn't require.
- Assuming async replica lag is zero and not measuring actual RPO in production.

## Principal-Level Questions
- When would you choose Cloud Spanner over a regional Cloud SQL instance with cross-region replicas for a DR-sensitive workload?
- How do you validate RPO claims are actually being met?

## Scenario Question
> Design DR for a global-scale application requiring active-active writes across 2 regions with strict consistency.

## Strong Answer (Outline)
1. Cloud Spanner as the data layer — purpose-built for exactly this requirement (synchronous multi-region consistency), avoiding the significant custom engineering that active-active consistency would otherwise require on a regional database.
2. Global Load Balancer distributing traffic to both regions' application tier, each reading/writing to the same Spanner instance.
3. Regular chaos/failover testing simulating a full regional outage to validate actual application behavior, not just data-layer behavior.
4. Cost awareness: communicate Spanner's premium pricing against the business value of true active-active — validate the requirement is genuinely strict-consistency, not just "highly available."

## Follow-Up Questions
- What would you propose if the actual requirement turned out to be high availability rather than strict multi-region write consistency?
- How do you handle a downstream system that can't tolerate Spanner's specific consistency/latency characteristics?

## Trade-offs
- Cloud Spanner: strong consistency and DR posture, real cost and a more specialized operational/query model than standard relational databases.
