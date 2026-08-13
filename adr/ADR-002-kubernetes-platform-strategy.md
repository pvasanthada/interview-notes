# ADR-002 — Kubernetes Platform Strategy

**Status**: Accepted

## Context
Multiple application teams have independently stood up Kubernetes clusters with inconsistent configuration, no shared golden path, and duplicated operational effort (each team separately solving observability, ingress, and security baseline).

## Problem
Decide on a Kubernetes platform strategy: continue with independent per-team clusters, consolidate to shared clusters, or adopt a hybrid model.

## Options
1. **Continue independent clusters per team** — maximum team autonomy, high duplicated operational cost, inconsistent security posture.
2. **Fully consolidated shared clusters** (few large clusters, namespace-per-team multi-tenancy) — lowest operational overhead, requires strong multi-tenant isolation (RBAC, NetworkPolicy per [`kubernetes/security-rbac.md`](../kubernetes/security-rbac.md)) and careful noisy-neighbor management.
3. **Hybrid — shared platform tooling, per-environment or per-business-unit clusters** — balances isolation and operational efficiency.

## Decision
Adopt the hybrid model: shared platform tooling (GitOps, observability, golden-path modules) applied consistently, with clusters split by environment and business-unit risk tier rather than either fully independent or fully consolidated.

## Rationale
- Fully independent clusters duplicate significant operational effort across teams with no corresponding benefit for most workloads.
- Fully consolidated clusters concentrate blast radius and multi-tenancy risk beyond what's justified for genuinely different-risk-tier workloads (see [`kubernetes/security-rbac.md`](../kubernetes/security-rbac.md)).
- The hybrid model captures most of the operational efficiency of consolidation (shared tooling, shared platform team expertise) while preserving meaningful isolation boundaries where risk profile actually differs.

## Consequences
- Platform team builds and owns shared GitOps, observability, and golden-path tooling (see [`kubernetes/helm-gitops.md`](../kubernetes/helm-gitops.md), [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md)) applied consistently across all clusters.
- Application teams no longer independently operate cluster-level infrastructure; they consume the platform via self-service within their assigned cluster/namespace.
- Existing independent clusters require a migration plan onto the new model.

## Risks
- Migration of existing clusters carries workload disruption risk if not carefully sequenced.
- The hybrid model still requires a clear, documented rubric for which workloads share a cluster versus warrant their own — an ambiguous rubric could recreate the original inconsistency at a different scale.
