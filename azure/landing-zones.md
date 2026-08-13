# Azure Landing Zones

**Label:** `Reference Architecture` / `Learning-Overview` — mark actual depth honestly per your real experience.

## What It Is

An Azure Landing Zone is the pre-provisioned, policy-governed environment a workload lands into: management group hierarchy, subscription structure, networking topology, identity baseline, policy assignments, and shared platform services (logging, security tooling). Microsoft's Cloud Adoption Framework (CAF) defines reference **archetypes** (e.g., "Corp," "Online," "Sandbox") rather than a single fixed template.

## Why Architects Use It

- Provides a consistent, governed starting point so application teams don't each reinvent identity, networking, and policy from scratch.
- Separates **platform** concerns (connectivity, identity, management) from **workload** concerns (application subscriptions), which lets platform and application teams move independently.
- Embeds guardrails (policy-as-code) so compliance is enforced structurally, not by review.

## Architecture Considerations

- **Management group hierarchy**: typically Root → Platform / Landing Zones / Sandbox / Decommissioned, with Platform split into Identity, Management, Connectivity.
- **Subscription democratization**: subscriptions should be cheap to provision (subscription vending) so teams aren't cramming unrelated workloads into one subscription for lack of process.
- **Landing zone archetypes**: "Corp" (workloads needing private connectivity to on-prem/hub) vs "Online" (internet-facing workloads with lighter connectivity requirements) — choose per workload, not one-size-fits-all.
- **Policy inheritance**: policies assigned at management group level cascade down; design the hierarchy so policy intent maps cleanly to grouping (don't fight the hierarchy with exclusions everywhere).
- **Identity foundation**: centralized Entra ID tenant, PIM for privileged access, no standing owner-level access to workload subscriptions.
- **Shared platform services**: centralized logging (Log Analytics), centralized DNS, centralized firewall/egress, break-glass accounts.

## Common Mistakes

- Treating landing zone design as a one-time project instead of an evolving platform with its own backlog.
- Putting too many workloads in too few subscriptions, making blast radius and cost attribution unmanageable.
- Over-restricting policy so teams route around it (shadow IT), rather than building enablement (self-service) alongside guardrails.
- Copying Microsoft's reference architecture literally without adapting it to actual organizational structure and compliance requirements.
- No clear subscription lifecycle (creation, ownership, decommissioning) — leads to orphaned/undermanaged subscriptions over time.

## Principal-Level Questions

- How would you design a landing zone strategy for a regulated financial services company operating in three regions?
- How do you decide the boundary between what belongs in the platform landing zone vs. what workload teams own?
- How would you migrate an organization from a flat, ungoverned subscription sprawl into a landing zone model without a "big bang" cutover?

## Scenario Question

> A newly acquired subsidiary has 40 ungoverned Azure subscriptions with inconsistent naming, no policy, and shared owner-level access across teams. Design a plan to bring it under your landing zone model within two quarters without breaking existing production workloads.

## Strong Answer (Outline)

1. **Assess & inventory** — automated discovery of subscriptions, resources, RBAC assignments, and policy state before touching anything.
2. **Define target state** — management group hierarchy, archetype mapping per workload, identity model, network connectivity model.
3. **Non-disruptive first steps** — move subscriptions into the correct management group (this alone doesn't break workloads), begin applying audit-mode (not enforce-mode) policies to surface gaps without breaking anything.
4. **Identity remediation** — replace shared owner access with PIM-eligible roles; this is usually the highest-risk, highest-value early win.
5. **Networking remediation** — plan a phased move to hub-spoke/Virtual WAN connectivity for workloads that need it; leave isolated workloads alone if they don't need shared connectivity yet.
6. **Enforce policy incrementally** — move from audit to deny mode workload-group by workload-group, with clear owner communication and exception process.
7. **Governance cadence** — establish a recurring review, subscription vending process, and clear escalation for exceptions.

## Follow-Up Questions

- How would you handle a workload team that refuses to comply with the new policy set because it breaks their existing pipeline?
- How do you decide when a workload gets its own subscription vs. shares one?
- What telemetry would tell you the landing zone rollout is actually succeeding?

## Trade-offs

- **Strict archetypes vs. flexibility**: rigid archetypes are easier to govern but can frustrate legitimate edge cases — plan an exception process, don't just say no.
- **Centralized platform team vs. self-service**: centralizing everything creates a bottleneck; too much self-service without guardrails creates drift. The answer is self-service *within* guardrails (see [`platform-engineering/`](../platform-engineering/)).
- **Speed of rollout vs. risk**: big-bang policy enforcement is fast but risky; phased audit-then-enforce is slower but far safer for existing production workloads.
