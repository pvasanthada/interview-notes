# Scenario: Large-Scale Cloud Migration

**Label:** `Interview Preparation`

## Prompt
> A company with 300 applications running in two on-premises datacenters needs to migrate to Azure within 18 months due to a datacenter lease expiration. Plan the migration.

## Context
On-prem estate with mixed application ages/architectures, a hard deadline (lease expiration, not flexible), and 300 applications — too many to migrate individually with the same care.

## Problem
Migrate a large, heterogeneous estate within a fixed timeline without a full inventory of what each application actually needs, while minimizing business disruption and avoiding a "lift-and-shift everything" outcome that misses cloud's actual value.

## Analysis
- Not all 300 applications carry equal business value or technical complexity — treating them uniformly wastes effort on low-value systems and under-invests in critical ones.
- The hard deadline argues against attempting a full application-by-application modernization for everything; some pragmatic lift-and-shift is likely necessary for lower-priority systems.
- Landing zone foundation (see [`azure/landing-zones.md`](../azure/landing-zones.md)) must exist before meaningful migration begins — migrating into an ungoverned environment just moves the governance debt into the cloud.

## Options
1. **Full modernization for every app** — best long-term architecture, unrealistic within 18 months for 300 applications.
2. **Full lift-and-shift for every app** — fastest, meets the deadline, captures little of cloud's actual benefit and risks high ongoing cost (see [`architecture/cloud-design.md`](../architecture/cloud-design.md)).
3. **Portfolio-tiered approach (recommended)** — classify applications (using a framework like Rehost/Replatform/Refactor/Retire/Retain — the "5 Rs") and apply the right-sized approach per tier, driven by business value and technical complexity.

## Decision
Adopt the portfolio-tiered approach:
1. **Discovery & assessment (months 1–2)**: automated discovery tooling plus stakeholder interviews to classify all 300 applications by business criticality, technical complexity, and dependencies.
2. **Landing zone build (months 1–3, parallel)**: stand up the target Azure landing zone (per [`azure/landing-zones.md`](../azure/landing-zones.md)) so it's ready before migration waves begin.
3. **Retire/Retain decisions first**: identify applications to decommission (often 10–20% of a legacy estate) or retain on-prem temporarily via a colocation bridge if genuinely necessary — this immediately shrinks the actual migration scope.
4. **Wave-based migration (months 3–16)**: lift-and-shift (rehost) for low-complexity/lower-value applications to hit the timeline; replatform (e.g., move a self-managed database to a managed service) for moderate-value applications where the effort is justified; reserve full refactor for the small number of highest-value, most cloud-benefit-sensitive applications, potentially continuing past the 18-month deadline on a bridged basis.
5. **Buffer & validation (months 16–18)**: reserved time for the inevitable long-tail issues, final cutover validation, and datacenter decommissioning.

## Trade-offs
- This approach accepts that most applications won't be "fully cloud-native" by the deadline — an explicit, communicated trade-off against the alternative of missing the deadline entirely or attempting unrealistic full modernization.
- Retire/Retain decisions require real stakeholder negotiation and can be organizationally harder than the technical migration itself.

## Implementation
- Establish a migration factory model: a core team building repeatable migration patterns/tooling (per application tier), with application-owning teams executing against those patterns rather than the central team migrating all 300 applications individually.
- Track progress against the wave plan with clear go/no-go criteria per wave, not just calendar milestones.

## Outcome (Illustrative)
A staged migration completing rehost/replatform for the majority of the estate within the deadline, with a documented, funded plan for the remaining refactor-tier applications to continue post-deadline via a negotiated datacenter bridge for the final few systems.

## Lessons Learned
- Discovery/assessment is consistently underestimated in timeline planning — it should start immediately and run in parallel with landing zone build, not sequentially before it.
- A portfolio-tiered approach, communicated clearly to leadership early, prevents the false binary of "modernize everything" vs. "lift-and-shift everything."
