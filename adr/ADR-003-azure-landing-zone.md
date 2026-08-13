# ADR-003 — Azure Landing Zone Architecture

**Status**: Accepted

## Context
The organization is expanding its Azure footprint from a handful of ungoverned subscriptions to an anticipated 40+ subscriptions across multiple business units within 18 months.

## Problem
Establish a landing zone architecture before subscription sprawl makes retrofitting governance significantly more disruptive.

## Options
1. **Adopt Microsoft's Cloud Adoption Framework Landing Zone reference architecture as-is.**
2. **Design a fully custom landing zone from scratch.**
3. **Adopt the CAF reference architecture as a starting point, adapted to organizational structure and compliance requirements** (recommended).

## Decision
Adopt option 3: CAF-based landing zone, adapted for this organization's specific business-unit structure and regulatory requirements (see [`azure/landing-zones.md`](../azure/landing-zones.md)).

## Rationale
- The CAF reference architecture encodes substantial industry best practice and reduces design time versus starting fully custom.
- A fully custom design risks reinventing well-solved problems and diverging from patterns most new hires/consultants will already be familiar with.
- Literal, unmodified adoption risks a mismatch with this organization's specific business-unit and compliance structure — adaptation is necessary, not optional.

## Consequences
- Management group hierarchy: Platform (Identity/Management/Connectivity) / Landing Zones (Corp/Online archetypes) / Sandbox / Decommissioned.
- Subscription vending process automated via Terraform (see [`terraform/modules.md`](../terraform/modules.md)) rather than manual provisioning.
- Existing ungoverned subscriptions require a phased migration into the new hierarchy (audit-then-enforce policy rollout per [`azure/governance-policy.md`](../azure/governance-policy.md)).

## Risks
- Migrating existing subscriptions into the new hierarchy carries risk of breaking existing workloads if policy enforcement is rushed — mitigated by the audit-before-enforce discipline.
- The archetype model must be revisited periodically as business-unit structure evolves, or it will drift out of alignment.
