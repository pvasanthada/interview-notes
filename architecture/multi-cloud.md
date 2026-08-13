# Multi-Cloud Architecture

**Label:** `Reference Architecture`

## What It Is
Architecture spanning more than one cloud provider — ranging from genuinely portable, cloud-agnostic workloads to organizations simply using different clouds for different, non-interoperating purposes (sometimes called "multi-cloud by accident" rather than by design).

## Why Architects Use It
Reasons range from genuine strategic need (avoiding vendor lock-in for a specific critical capability, regulatory requirements, best-of-breed service selection) to organic organizational reality (an acquisition brought a different cloud, or different teams made different choices) — a principal architect should be able to distinguish deliberate multi-cloud strategy from unmanaged multi-cloud sprawl and design appropriately for each.

## Architecture Considerations
- **Multi-cloud for resilience vs. multi-cloud for portability vs. multi-cloud by accident**: each has a different appropriate architecture response — resilience-driven multi-cloud needs active failover design; portability-driven needs abstraction layers (e.g., Kubernetes as a common substrate, Terraform as a common IaC layer); accidental multi-cloud often just needs consistent governance applied per-cloud rather than forced technical unification.
- **The abstraction-layer trade-off**: building a full cloud-agnostic abstraction layer (avoiding any provider-specific service) sacrifices the best-of-breed managed services each cloud offers in exchange for portability — this is rarely worth it unless portability is a genuine, high-priority requirement (e.g., specific regulatory or contractual mandate), since most organizations end up needing provider-specific capabilities somewhere anyway.
- **Consistent governance across clouds**: identity (see [`security/iam.md`](../security/iam.md)), observability (see [`observability.md`](observability.md)), and cost management (see [`finops.md`](finops.md)) should be unified across clouds even when the underlying workloads aren't portable — this is usually higher-value than workload portability itself.
- **Network connectivity between clouds**: when workloads in different clouds genuinely need to communicate, design explicit, secured connectivity (VPN/dedicated interconnect between clouds) rather than routing through the public internet by default.

## Common Mistakes
- Building a full cloud-agnostic abstraction layer "just in case," sacrificing velocity and best-of-breed services for a portability requirement that may never materialize.
- Treating "multi-cloud" as inherently more resilient without actually designing active cross-cloud failover — simply having workloads in two clouds that don't fail over to each other isn't multi-cloud resilience, it's just cost and complexity.
- Inconsistent governance/security posture across clouds because ownership sits with separate teams with different maturity levels.

## Principal-Level Questions
- When is a genuine multi-cloud strategy justified, versus when is "just pick one cloud and go deep" the better answer?
- How would you unify governance across two clouds owned by historically separate teams?

## Scenario Question
> Leadership asks whether the organization should adopt an explicit multi-cloud strategy for resilience, citing a competitor's major cloud provider outage last year.

## Strong Answer (Outline)
1. Clarify the actual goal: "resilience against a full cloud provider outage" is a very high bar that most organizations don't actually need to meet — single-region-within-a-cloud outages are far more common and are addressed by multi-region design within one cloud, at much lower cost/complexity than true multi-cloud active-active.
2. If a genuine, business-justified requirement for provider-outage resilience exists (e.g., a specific regulatory mandate or an extremely high-value, latency-tolerant workload), scope it narrowly to that specific workload rather than mandating multi-cloud broadly across the estate — full-estate multi-cloud is a very expensive, very complex undertaking.
3. Regardless of the resilience decision, recommend unifying governance (identity, observability, cost) across whatever clouds are already in use, since this is high-value regardless and is a more common, more solvable gap than the provider-outage scenario.
4. Present the cost/complexity trade-off explicitly to leadership so the decision is made with full information, not based on a single headline incident.

## Follow-Up Questions
- How would you scope a narrow, justified multi-cloud resilience solution for one specific critical workload?
- What would change your recommendation if the organization already had a regulatory mandate for multi-cloud?

## Trade-offs
- Full multi-cloud active-active: genuine provider-outage resilience, very high cost/complexity, rarely justified relative to the actual likelihood/impact of a full-provider outage versus more common regional failure modes.
