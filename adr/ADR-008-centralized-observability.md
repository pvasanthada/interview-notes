# ADR-008 — Centralized Observability

**Status**: Accepted

## Context
The organization's platform spans Azure, GCP, and multiple Kubernetes clusters (per ADR-002), each currently using separate, cloud-native observability tooling with no unified cross-platform view.

## Problem
Design an observability strategy giving unified visibility across clouds and clusters without abandoning each platform's mature native tooling.

## Options
1. **Fully replace native tooling with a single third-party observability platform** — unified from day one, high migration cost/risk and loses some cloud-native integration depth.
2. **Keep fully siloed native tooling per platform** — lowest near-term cost, no cross-platform visibility, slow incident correlation across boundaries.
3. **Standardize instrumentation (OpenTelemetry) and federate/aggregate telemetry into a unified layer, while preserving native tooling for local debugging** (recommended, see [`architecture/observability.md`](../architecture/observability.md)).

## Decision
Standardize application instrumentation on OpenTelemetry and introduce a federated aggregation layer (long-term metrics storage plus centralized log/trace correlation) while retaining native per-platform tooling for local, low-latency debugging.

## Rationale
- OpenTelemetry avoids vendor lock-in at the instrumentation layer and works consistently regardless of the eventual choice of backend/aggregation tooling (see [`kubernetes/observability.md`](../kubernetes/observability.md)).
- A full tooling replacement carries unacceptable near-term migration risk and cost relative to the incremental benefit over a well-designed federation approach.
- Fully siloed tooling directly conflicts with the stated goal of faster cross-platform incident correlation, which is the actual problem driving this decision.

## Consequences
- All new services instrumented with OpenTelemetry SDKs from day one; existing services migrated incrementally, prioritized by criticality.
- SLOs defined per critical user journey, spanning whichever platforms that journey actually depends on end to end (see [`architecture/observability.md`](../architecture/observability.md)).
- Platform team owns and operates the federated aggregation layer as a shared platform capability (see [`platform-engineering/platform-architecture.md`](../platform-engineering/platform-architecture.md)).

## Risks
- The federated aggregation layer itself becomes a critical shared dependency requiring its own resilience design.
- Incremental migration of existing services means the unified view will have gaps until migration completes — communicated explicitly as a phased rollout, not a day-one complete capability.
