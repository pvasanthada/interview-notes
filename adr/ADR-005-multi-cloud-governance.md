# ADR-005 — Multi-Cloud Governance

**Status**: Accepted

## Context
Following ADR-003 and ADR-004, the organization now operates governed landing zones on both Azure and GCP, each with cloud-native policy enforcement (Azure Policy, GCP Organization Policy) but no unified cross-cloud governance visibility.

## Problem
Decide how to govern consistently across two clouds without forcing a single, cloud-agnostic policy engine that would sacrifice each cloud's native governance maturity.

## Options
1. **Build/adopt a fully cloud-agnostic policy engine spanning both clouds** — theoretically elegant, in practice sacrifices the depth of each cloud's native tooling and adds a new system to operate.
2. **Use each cloud's native policy engine independently, unified only at the reporting/visibility layer** (recommended) — preserves native tooling depth, requires an aggregation layer for unified visibility.

## Decision
Use native policy engines per cloud (Azure Policy, GCP Organization Policy), unified through a centralized compliance reporting layer aggregating findings from both, rather than a single cross-cloud policy engine.

## Rationale
- Native policy engines are more mature and better integrated with their respective cloud's resource model than a third-party or custom cross-cloud abstraction would be.
- The actual governance goal — consistent risk posture and unified visibility for leadership/security — is achievable at the reporting layer without sacrificing native tooling depth (mirrors the reasoning in [`architecture/multi-cloud.md`](../architecture/multi-cloud.md)).

## Consequences
- Both clouds' compliance/CSPM findings (Defender for Cloud, Security Command Center) feed into a centralized SIEM/reporting platform (see [`security/security-governance.md`](../security/security-governance.md)) for unified visibility.
- Policy authoring remains cloud-specific, requiring platform engineers to maintain policy expertise in both native systems.
- A shared governance framework document maps equivalent policy intents across both clouds (see [`comparison-tables.md`](../comparison-tables.md)) so policy coverage can be compared for gaps.

## Risks
- Maintaining policy parity across two distinct native systems requires ongoing discipline to avoid drift (one cloud's policy set becoming stronger/weaker than the other's over time).
- Reporting-layer aggregation adds its own operational dependency that must itself be monitored for reliability.
