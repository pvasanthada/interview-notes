# ADR-004 — GCP Landing Zone Architecture

**Status**: Accepted

## Context
Following ADR-003's Azure landing zone decision, the organization has also acquired a GCP-based subsidiary and needs a consistent landing zone approach for GCP (see [`gcp/landing-zones.md`](../gcp/landing-zones.md)).

## Problem
Design a GCP landing zone that mirrors the governance intent of the Azure landing zone (ADR-003) while respecting GCP's distinct resource hierarchy and networking model.

## Options
1. **Force a structurally identical hierarchy to Azure** (e.g., map management groups to folders 1:1 without adapting to GCP's "many small projects" philosophy) — easier cross-cloud mental mapping, fights against GCP's native design patterns.
2. **Design natively for GCP's model**, accepting some structural difference from the Azure landing zone, unified only at the governance-intent level (identity, policy enforcement approach, logging destination) — recommended.

## Decision
Design natively for GCP's resource hierarchy and Shared VPC model, unifying with the Azure landing zone at the governance-intent level rather than forcing structural equivalence.

## Rationale
- GCP's "many small projects" pattern is a deliberate design choice that provides strong isolation benefits when embraced, not fought (see [`gcp/landing-zones.md`](../gcp/landing-zones.md)).
- Forcing structural equivalence with Azure would require working against GCP's native tooling and idioms, increasing operational friction for the team managing it.
- Cross-cloud consistency matters most at the governance-intent level (see [`security/iam.md`](../security/iam.md), [`architecture/observability.md`](../architecture/observability.md)) — federated identity, consistent least-privilege discipline, unified logging destination — not at the level of literal structural mirroring.

## Consequences
- GCP folder structure: environment-based (Production / Non-Production / Sandbox), automated project vending via Terraform.
- Identity federated from the same central Entra ID tenant used for Azure (see [`security/iam.md`](../security/iam.md)).
- Log sinks configured to export into the same centralized logging/SIEM destination used for Azure, for unified cross-cloud visibility.

## Risks
- Teams familiar only with Azure's model may need onboarding to GCP's distinct resource hierarchy — mitigated by clear documentation (this repository's [`gcp/`](../gcp/) folder) explicitly calling out the conceptual mapping and its limits.
