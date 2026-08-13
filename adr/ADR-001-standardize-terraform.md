# ADR-001 — Standardize on Terraform for Infrastructure as Code

**Status**: Accepted

## Context
The organization operates across Azure and GCP with infrastructure currently provisioned through a mix of manual console changes, cloud-native IaC (ARM templates on Azure, Deployment Manager on GCP), and ad hoc scripts. This inconsistency makes cross-cloud automation, review, and governance difficult.

## Problem
Choose a single infrastructure-as-code approach that can be applied consistently across both clouds, supported by a common team skillset and CI/CD pipeline pattern.

## Options
1. **Cloud-native IaC per cloud** (ARM/Bicep for Azure, Deployment Manager for GCP) — first-day feature support, no cross-cloud consistency, doubles the tooling/skillset the team must maintain.
2. **Terraform** — one tool, one language (HCL), one CI/CD pattern across both clouds, occasionally slightly behind on very new service support.
3. **Custom internal tooling** — full control, high build/maintenance cost, not justified given mature existing options.

## Decision
Standardize on Terraform as the primary IaC tool across both clouds.

## Rationale
- Single skillset and workflow reduces onboarding cost and cross-team friction (see [`terraform/README.md`](../terraform/README.md)).
- Strong ecosystem (modules, providers, community) for both Azure and GCP.
- Enables consistent policy-as-code (OPA/Conftest) and CI/CD patterns across the whole estate (see [`terraform/policy-as-code.md`](../terraform/policy-as-code.md)).
- The occasional lag on brand-new service support is an acceptable trade given the consistency benefit; teams can use a native-tool escape hatch for a specific new-service gap while the Terraform provider catches up.

## Consequences
- All new infrastructure work standardizes on Terraform going forward.
- Existing ARM/Deployment Manager configurations require a migration plan (not necessarily immediate) — prioritized by risk/change-frequency, not a big-bang rewrite.
- Platform team invests in a shared module library (see [`terraform/modules.md`](../terraform/modules.md)) as the next phase of this decision.

## Risks
- Migration effort for existing native-IaC resources is non-trivial and needs its own prioritized plan.
- Provider lag for brand-new cloud services could occasionally block using a new feature immediately at GA — mitigated by an documented, reviewed exception process for using native tooling temporarily in that specific case.
