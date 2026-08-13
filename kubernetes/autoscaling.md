# Kubernetes Autoscaling

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Horizontal Pod Autoscaler (HPA — scales replica count based on metrics), Vertical Pod Autoscaler (VPA — adjusts pod resource requests/limits), Cluster Autoscaler (adds/removes nodes based on pending/underutilized capacity), and Pod Disruption Budgets (PDBs — constrain voluntary disruption during scaling/maintenance).

## Why Architects Use It
Correctly configured autoscaling is what makes Kubernetes cost-efficient and resilient to load variance; misconfigured autoscaling is one of the most common sources of both wasted spend and production incidents.

## Architecture Considerations
- **HPA metric choice**: CPU-based HPA is the default but often the wrong signal for I/O-bound or queue-driven workloads — custom metrics (queue depth, request latency) via metrics adapters often better reflect true load.
- **HPA + VPA together**: generally avoid running both on the same workload with overlapping resource dimensions (they can fight each other); VPA is often better suited to right-sizing over time while HPA handles real-time replica scaling.
- **Cluster Autoscaler vs. node-level autoscaling (e.g., AKS/GKE node auto-provisioning)**: understand the interaction with PDBs — the Cluster Autoscaler respects PDBs when scaling down, so an overly strict PDB (e.g., minAvailable equal to replica count) can block node scale-down entirely.
- **PDBs**: protect availability during voluntary disruptions (node drains, upgrades) — set `minAvailable`/`maxUnavailable` based on the actual availability requirement, not reflexively at the most conservative value.

## Common Mistakes
- CPU-only HPA on workloads whose actual bottleneck is downstream (database, queue) — scaling replicas doesn't help and can make things worse (more connections hitting an already-saturated dependency).
- No PDB at all, allowing a node drain/upgrade to take down all replicas of a service simultaneously.
- PDB set so conservatively it blocks legitimate cluster upgrades/scale-down indefinitely.
- Running HPA and VPA on the same resource dimension without understanding the conflict.

## Principal-Level Questions
- How would you design autoscaling for a workload whose real bottleneck is a downstream database, not CPU?
- How do PDBs interact with Cluster Autoscaler scale-down, and what happens if they're misconfigured?

## Scenario Question
> A service scales horizontally under load per its HPA, but response times keep degrading anyway, and the downstream database connection pool is maxed out.

## Strong Answer (Outline)
1. Recognize this as a case where the bottleneck is downstream, not the service's own compute — CPU-based HPA is scaling a symptom, not the cause, and may be actively worsening the database contention.
2. Cap the HPA's maximum replica count to a safe ceiling relative to database connection pool capacity, or introduce connection pooling (e.g., PgBouncer) as an intermediary.
3. Consider a queue-based buffering pattern to smooth bursty load into the database rather than let it scale directly with request replicas.
4. Address the actual bottleneck (database read replicas, caching layer, query optimization) as the real fix — the autoscaling and pooling changes are mitigations, not the root-cause fix.

## Follow-Up Questions
- How would you detect this class of problem proactively rather than during an incident?
- How does your answer change if the downstream dependency is a third-party API with its own rate limits?

## Trade-offs
- Aggressive autoscaling: better responsiveness to load, can amplify downstream bottlenecks if not bounded.
- Conservative PDBs: better availability during disruption, can block legitimate node maintenance/scale-down if too strict.
