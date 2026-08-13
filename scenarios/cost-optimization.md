# Scenario: Cost Optimization

**Label:** `Interview Preparation`

## Prompt
> A Kubernetes platform's cloud costs have grown 3x over the past year while the number of deployed workloads has only grown 1.5x. Diagnose and remediate.

## Context
Shared Kubernetes platform, cost growth outpacing workload growth — a strong signal of inefficiency rather than purely organic scale growth (see [`architecture/finops.md`](../architecture/finops.md)).

## Problem
Identify the actual sources of disproportionate cost growth and remediate without disrupting running workloads or degrading reliability.

## Analysis
A 2:1 mismatch between cost growth and workload growth points to per-workload inefficiency, not just scale — likely candidates: over-provisioned resource requests, under-utilized autoscaling configuration, orphaned resources, or a specific workload class (e.g., a recently added GenAI feature, per [`ai-genai/ai-architecture.md`](../ai-genai/ai-architecture.md)) driving disproportionate spend.

## Options
1. Broad, immediate cost-cutting (across-the-board resource limit reduction) — fast, risky, likely to cause reliability incidents from under-provisioning workloads that genuinely need their current resources.
2. Investigate before acting (recommended) — slower to show initial results, much lower risk, and targets the actual sources.

## Decision
1. **Establish cost attribution**: ensure Kubernetes cost allocation (namespace/label-based, per [`architecture/finops.md`](../architecture/finops.md)) is in place so spend can be attributed to specific teams/workloads — without this, remediation is guesswork.
2. **Identify the disproportionate driver(s)**: compare per-namespace cost growth against per-namespace workload growth to find the specific outliers rather than assuming uniform inefficiency across the whole platform.
3. **Common findings to check**: resource requests set far above actual utilization (`kubectl top` / utilization metrics vs. requested), Cluster Autoscaler not scaling down due to overly conservative PodDisruptionBudgets (per [`kubernetes/autoscaling.md`](../kubernetes/autoscaling.md)), orphaned PersistentVolumes/LoadBalancer Services from decommissioned workloads never cleaned up, and any new AI/GenAI workload with unbounded token usage (per [`ai-genai/ai-architecture.md`](../ai-genai/ai-architecture.md)).
4. **Remediate per finding**: rightsize resource requests based on actual utilization data (with a safety margin, validated via monitoring after the change, not blindly cut); relax overly-conservative PDBs where safe to restore Cluster Autoscaler scale-down; clean up orphaned resources; apply cost controls (caching, model-tier routing) to any AI workload found to be the outlier.
5. **Prevent recurrence**: add resource request/limit review as part of the platform's golden path (per [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md)) and ongoing cost dashboards per team, not a one-time audit.

## Trade-offs
- Rightsizing resource requests carries some risk of under-provisioning if utilization data doesn't capture genuine peak load patterns — validate changes with continued monitoring and a rollback plan, not a one-time blind cut.

## Implementation
Sequence: cost attribution (weeks) → outlier identification (weeks) → targeted remediation per finding (ongoing) → golden-path/dashboard prevention (ongoing).

## Outcome (Illustrative)
Investigation finds 40% of the cost growth traces to a single team's overly conservative PDB blocking node scale-down, and 30% to a new GenAI feature with no caching. Remediating both largely closes the gap without broad, risky cuts elsewhere.

## Lessons Learned
- Cost growth disproportionate to usage growth is almost always attributable to a small number of specific outliers, not uniform platform-wide inefficiency — investigation targeting those outliers is far more effective than broad, indiscriminate cost-cutting.
