# FinOps — Cloud Financial Management

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The operating model (and increasingly its own discipline) for managing cloud spend collaboratively across finance, engineering, and business teams — cost visibility, allocation, forecasting, and optimization, applied continuously rather than as a periodic cost-cutting exercise.

## Why Architects Use It
Cloud's consumption-based pricing means cost is a direct, continuous function of architectural and operational decisions in a way traditional fixed-capacity infrastructure spend wasn't — architects who don't factor cost into design decisions produce technically sound but financially unsustainable systems.

## Architecture Considerations
- **Cost visibility and allocation**: consistent tagging/labeling (mapped to team/product/environment) is the foundation everything else depends on — without it, cost optimization efforts can't target the actual source of spend growth.
- **Rightsizing and autoscaling**: match provisioned capacity to actual load continuously, not as a one-time exercise — over-provisioned "just in case" capacity is one of the most common and highest-value optimization targets.
- **Reserved/committed capacity**: for predictable, steady-state workloads, reserved instances/committed use discounts trade flexibility for meaningful discount — a deliberate trade-off requiring reasonably confident capacity forecasting.
- **Kubernetes cost allocation**: shared cluster costs need namespace/label-based cost attribution (not just node-level cost) to give teams accurate signal for their actual consumption — a common gap in Kubernetes cost visibility (see [`kubernetes/README.md`](../kubernetes/README.md)).
- **AI/LLM cost as a distinct, fast-growing category**: token-based pricing scales cost directly with usage in a way that needs the specific controls discussed in [`ai-genai/ai-architecture.md`](../ai-genai/ai-architecture.md) — caching, model-tier routing, usage attribution per feature.
- **Idle and orphaned resource cleanup**: unattached disks, idle load balancers, forgotten dev/test environments left running — a recurring, often underestimated cost drain requiring both automated detection and organizational process (lifecycle policies) to address sustainably.

## Common Mistakes
- Treating FinOps as a one-time cost-cutting project rather than an ongoing operating model with continuous visibility and shared ownership.
- No consistent tagging/labeling, making it impossible to attribute cost accurately enough to act on.
- Over-committing to reserved capacity based on optimistic growth projections that don't materialize, locking in cost without the offsetting usage.
- Treating cost optimization purely as an engineering/infrastructure activity, disconnected from actual business/product context about which spend is justified by value delivered.

## Principal-Level Questions
- How would you design a FinOps operating model that gives engineering teams real-time cost visibility and accountability, not just a monthly finance report?
- How would you control GenAI costs specifically, given their distinct pricing model?

## Scenario Question
> Leadership wants to reduce Azure spend by 25% without degrading reliability. Build the plan.

## Strong Answer (Outline)
1. Establish visibility first: ensure consistent tagging is in place (or fix it if not) to accurately attribute current spend by team/product/environment — you can't optimize what you can't see clearly.
2. Identify the highest-value, lowest-risk targets first: idle/orphaned resources (immediate, low-risk savings), over-provisioned capacity identifiable via utilization data (rightsizing, autoscaling adoption), and non-production environments that could be scheduled to shut down outside business hours.
3. Evaluate committed-capacity opportunities for genuinely steady-state, predictable workloads based on actual historical usage data, not optimistic projections.
4. For architectural-level changes (e.g., moving a workload to a more cost-efficient compute tier per [`azure/compute.md`](../azure/compute.md)), prioritize by savings-to-effort ratio and validate reliability impact via testing before rolling out broadly — this is where "without degrading reliability" needs explicit validation, not just an assumption.
5. Build ongoing visibility (dashboards, budget alerts per team) so the 25% reduction is sustained rather than eroding back over subsequent months — a one-time cost-cutting sprint without ongoing FinOps practice typically doesn't hold.

## Follow-Up Questions
- How would you handle a team that resists rightsizing because they're worried about a repeat of a past capacity-related incident?
- How do you balance aggressive cost optimization against genuine headroom needed for unpredictable growth/traffic spikes?

## Trade-offs
- Aggressive cost optimization: real savings, requires careful validation to avoid trading cost for reliability risk — the "without degrading reliability" constraint should shape *how* savings are achieved, not just *whether*.
