# Scenario: Kubernetes Production Failure

**Label:** `Interview Preparation`

## Prompt
> A routine Kubernetes cluster upgrade caused a 45-minute outage for a critical service, despite the service having 5 replicas and a PodDisruptionBudget configured. Diagnose and prevent recurrence.

## Context
Planned maintenance (upgrade) caused unplanned customer impact — a process/design gap, not a random failure (see [`kubernetes/architecture.md`](../kubernetes/architecture.md), [`kubernetes/autoscaling.md`](../kubernetes/autoscaling.md)).

## Problem
Understand why redundancy (5 replicas) and a safety mechanism (PDB) didn't prevent the outage, and fix the underlying gap before the next upgrade.

## Analysis
5 replicas and a PDB should, in principle, allow a rolling node drain without full service loss — the fact that it didn't points to a specific gap: either the PDB was misconfigured (e.g., `minAvailable` set too low, or matching the wrong label selector so it didn't actually apply to this workload), the readiness probe was misconfigured (new pods marked ready before actually able to serve traffic, so "replacement" pods weren't truly available), or all 5 replicas happened to be scheduled such that a single node's drain took out a disproportionate share (e.g., no pod anti-affinity spreading them across nodes/zones).

## Options
This is a diagnostic scenario — the "options" are hypotheses to systematically rule in/out using [`kubernetes/troubleshooting.md`](../kubernetes/troubleshooting.md)'s method:
1. PDB misconfiguration or wrong selector.
2. Readiness probe misconfiguration.
3. No pod anti-affinity, causing concentrated node placement.
4. Cluster Autoscaler/node pool capacity insufficient to schedule replacement pods before old ones were terminated.

## Decision (Diagnostic Approach)
1. Check the PDB definition against the workload's actual labels first — a mismatched selector is a very common, easy-to-miss root cause where the PDB silently applies to zero pods.
2. Check the readiness probe configuration — if it doesn't actually validate the application is ready to serve traffic (e.g., only checks process liveness), new pods get marked ready and receive traffic before they're truly able to serve it, causing errors during the rollout window even with enough replica count.
3. Check pod placement (`kubectl get pods -o wide`) at the time of the incident — if all 5 replicas were concentrated on a small number of nodes due to no anti-affinity/topology spread constraints, draining even one or two of those nodes could take out a disproportionate share simultaneously.
4. Check whether the Cluster Autoscaler had headroom to schedule replacement pods before old ones were terminated — if node capacity was tight, the upgrade process might have been forced to terminate old pods before new ones were schedulable elsewhere.

## Remediation (Once Root-Caused)
- Fix the specific misconfiguration found (PDB selector, readiness probe, anti-affinity/topology spread constraints, or autoscaler headroom).
- Regardless of the specific root cause, add topology spread constraints as a general resilience improvement, and validate PDB/readiness probe correctness as a pre-upgrade checklist item, not something assumed correct.
- Conduct upgrades on a lower-risk cluster/environment first as a rehearsal, with the same monitoring in place, before the production upgrade.

## Trade-offs
- Stricter topology spread/anti-affinity constraints can reduce scheduling flexibility slightly (harder to pack pods as tightly), a reasonable trade for meaningfully improved resilience on a critical service.

## Outcome (Illustrative)
Root cause identified as a readiness probe checking only process liveness, not an actual dependency health check — new pods were marked ready and received traffic before their database connection pool had warmed up, causing errors during the rollout window that were misattributed to "not enough replicas."

## Lessons Learned
- Redundancy (replica count) alone doesn't guarantee availability during rolling changes — readiness probe correctness is equally critical and is a much less commonly scrutinized configuration.
- A pre-upgrade checklist validating PDB, readiness probes, and pod placement should become a standard practice before any cluster upgrade on critical workloads.
