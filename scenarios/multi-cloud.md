# Scenario: Multi-Cloud Integration

**Label:** `Interview Preparation`

## Prompt
> Following an acquisition, your Azure-centric organization must integrate an acquired company that runs entirely on GCP. Leadership wants a unified platform within 12 months, but hasn't decided whether to migrate the acquired company to Azure or genuinely operate multi-cloud long-term.

## Context
Organic multi-cloud situation from an acquisition (see [`architecture/multi-cloud.md`](../architecture/multi-cloud.md)) with an open strategic question and a defined timeline for at least partial integration.

## Problem
Deliver meaningful integration value within 12 months without prematurely committing to (or foreclosing) the long-term single-cloud-vs-multi-cloud decision, which isn't yet made.

## Analysis
The two possible long-term outcomes (full migration to Azure vs. genuine ongoing multi-cloud) both benefit from the same near-term foundational work: unified identity, unified observability, and unified governance (per [`architecture/multi-cloud.md`](../architecture/multi-cloud.md)) — this work is valuable regardless of the eventual strategic decision, so it can proceed now without waiting for that decision to be finalized.

## Options
1. Wait for the strategic decision before starting any integration work — safest against wasted effort, wastes the 12-month timeline waiting.
2. Commit immediately to full migration to Azure — fastest path to single-cloud simplicity if that's ultimately the right call, high wasted cost/risk if leadership later decides to keep GCP.
3. Build the strategy-agnostic foundation first, informing the eventual decision with real operational data (recommended).

## Decision
1. **Phase 1 (months 1–4): unify what's valuable regardless of outcome** — federate identity (per [`security/iam.md`](../security/iam.md)), establish cross-cloud observability (per [`architecture/observability.md`](../architecture/observability.md)), and bring the GCP estate under consistent governance/security posture (per [`gcp/governance.md`](../gcp/governance.md)) matching the parent organization's standards.
2. **Phase 2 (months 4–8, parallel with ongoing Phase 1 work): assess and inform the strategic decision** — evaluate the acquired GCP workloads' actual migration complexity/cost against the business case for keeping them on GCP (e.g., deep GCP-specific service dependencies, like Spanner or BigQuery, that would be costly to replicate on Azure) — bring this analysis to leadership as a data-informed input to the still-open strategic decision, rather than assuming the answer.
3. **Phase 3 (months 8–12): execute the decided path** — either begin a planned migration wave (per [`scenarios/cloud-migration.md`](cloud-migration.md)) or formalize the multi-cloud operating model (consistent tooling, cross-cloud network connectivity per [`gcp/networking.md`](../gcp/networking.md), ongoing governance) if leadership decides to keep GCP long-term.

## Trade-offs
- Deferring the migrate-vs-stay decision for several months means some integration work (e.g., final network connectivity architecture) can't be fully finalized immediately — an acceptable trade given that acting prematurely on an unmade strategic decision risks wasted rework.

## Implementation
Communicate this phased approach clearly to leadership as a plan, explicitly noting that Phase 1 delivers value under either eventual outcome — this framing helps leadership see the near-term work as low-risk regardless of when they finalize the strategic decision.

## Outcome (Illustrative)
By month 4, unified identity and observability are in place, meaningfully improving cross-organization operational visibility even before the strategic decision is made. The Phase 2 analysis reveals the acquired company's core platform is deeply dependent on BigQuery, informing leadership's decision to adopt a genuine long-term multi-cloud model rather than migrate.

## Lessons Learned
- In ambiguous, high-stakes strategic situations, look for the subset of work that's valuable under every plausible outcome and start there — it makes progress without betting on an unmade decision, and often produces exactly the data needed to inform that decision well.
