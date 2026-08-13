# Azure Storage

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
Azure Storage services — Blob (object), Files, Disks, Tables/Queues — with redundancy options (LRS/ZRS/GRS/GZRS) and access tiers (Hot/Cool/Cold/Archive).

## Why Architects Use It
Storage decisions directly drive cost, durability, and recovery posture; getting redundancy and tiering wrong is one of the most common silent cost/risk issues in cloud estates.

## Architecture Considerations
- **Redundancy selection**: LRS is cheapest but single-datacenter; ZRS protects against datacenter failure within a region; GRS/GZRS add cross-region replication for regional-disaster tolerance — choose based on actual RPO/RTO requirement, not by default maximizing redundancy everywhere.
- **Access tiering**: lifecycle-manage Hot → Cool → Archive automatically based on access patterns rather than manual tiering; Archive has real rehydration latency that must fit the workload's tolerance.
- **Private endpoints**: for anything holding sensitive data, disable public network access and use Private Link.
- **Immutability**: use immutable blob storage (WORM) for compliance-mandated retention (e.g., financial records).

## Common Mistakes
- Leaving everything in Hot tier regardless of access pattern, silently inflating cost.
- Choosing GRS by default for workloads that don't actually need cross-region durability, doubling storage cost unnecessarily.
- Public blob containers left open by misconfiguration — a very common real-world breach vector.

## Principal-Level Questions
- How would you design a cost-optimized storage tiering strategy for a platform generating large volumes of log data?
- What's your approach to preventing accidental public exposure of storage accounts at scale?

## Scenario Question
> Your platform generates 50TB/month of application logs, mostly queried only within the first 7 days. Design a cost-effective storage strategy.

## Strong Answer (Outline)
1. Hot tier for the first 7–14 days matching actual query pattern.
2. Lifecycle policy auto-transitions to Cool at day 14, Archive at day 90 (aligned to compliance retention requirement, e.g., 1–7 years).
3. Route active querying through a purpose-built log platform (Log Analytics) rather than querying raw blobs directly; blob storage is the durable, cheap long-term retention layer.
4. Enforce via Azure Policy that public access is disabled org-wide, with private endpoint access only.

## Follow-Up Questions
- How would you handle a legal hold requirement that conflicts with the lifecycle deletion policy?
- How do you estimate the cost trade-off between GRS and application-level replication?

## Trade-offs
- Redundancy: durability/availability vs. cost — GZRS is roughly 2x+ the cost of LRS.
- Aggressive archiving: cheaper storage vs. slower/rehydration-dependent access for older data.
