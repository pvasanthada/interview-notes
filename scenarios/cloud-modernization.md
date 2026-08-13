# Scenario: Application Modernization

**Label:** `Interview Preparation`

## Prompt
> A legacy monolithic application, lifted-and-shifted to Azure VMs two years ago, is now the biggest single driver of cloud cost and the slowest to deploy changes to. Plan its modernization.

## Context
Post-migration application running on fixed-capacity VMs, tightly coupled internally, deployed manually with a multi-week release cycle — the classic "lift-and-shift, never modernized" outcome (see [`architecture/cloud-design.md`](../architecture/cloud-design.md)).

## Problem
Reduce cost and improve deployment velocity without a risky full rewrite, while the application remains business-critical and must stay operational throughout.

## Analysis
- A full rewrite is high-risk and slow — the business can't accept a multi-quarter feature freeze on a critical system.
- The two biggest pain points (cost, deploy velocity) point to different root causes: cost likely stems from fixed-capacity provisioning; deploy velocity likely stems from tight coupling and manual deployment process, not necessarily the monolithic structure itself.
- Strangler fig pattern (incrementally extracting functionality into new services while the monolith continues operating) is generally lower-risk than a full rewrite for a business-critical system.

## Options
1. **Full rewrite/re-architecture** — best long-term outcome, highest risk and slowest time-to-value.
2. **Infrastructure-only modernization (autoscaling, containerization) without touching application structure** — addresses cost quickly, doesn't address deploy velocity's root cause (coupling).
3. **Strangler fig incremental extraction (recommended)** — addresses both concerns progressively with continuously realized value and manageable risk.

## Decision
1. **Quick win — infrastructure modernization first**: containerize the existing monolith as-is and move to an autoscaling platform (Container Apps or AKS, per [`azure/compute.md`](../azure/compute.md)) — this alone typically captures significant cost savings by matching capacity to actual load, without any application code change, and can happen within weeks.
2. **Establish CI/CD**: introduce automated build/test/deploy (see [`terraform/ci-cd.md`](../terraform/ci-cd.md) principles applied to application deployment) to reduce the manual, multi-week release cycle — a high-leverage change independent of the application's internal architecture.
3. **Identify extraction candidates**: analyze the monolith for the most change-frequent, most independently-valuable modules (often where the deploy pain is concentrated) and extract those first via the strangler fig pattern — routing an increasing share of traffic to new, independently-deployable services while the monolith continues handling the rest.
4. **Iterate**: continue extracting modules based on business value and pain, without a fixed end-state deadline — treat this as an ongoing capability improvement, not a one-time project with a defined "done."

## Trade-offs
- Strangler fig extraction is slower to reach a "fully modernized" end state than a rewrite, but delivers value continuously and keeps the business-critical system operational throughout — the right trade-off given the risk tolerance implied by "business-critical."
- Running both the monolith and extracted services temporarily increases operational complexity during the transition.

## Implementation
- Sequence: infrastructure modernization (weeks) → CI/CD (weeks, parallel) → first extraction (months) → ongoing iterative extraction.
- Each extraction is its own scoped, reversible change with clear rollback capability, not a single large cutover.

## Outcome (Illustrative)
Cost reduction realized within the first month from autoscaling alone; deploy cycle time reduced from weeks to days after CI/CD adoption; the highest-pain modules extracted within two quarters, with the remaining monolith stabilized rather than a source of ongoing pain.

## Lessons Learned
- Infrastructure modernization (autoscaling, CI/CD) often delivers the majority of near-term value with far less risk than structural application changes — sequence it first.
- Modernization is more sustainable framed as an ongoing capability than a project with a fixed "modernized" end state.
