# Observability — Cross-Cloud Strategy

**Label:** `Reference Architecture`

## What It Is
The cross-cutting practice of designing metrics, logs, traces, SLIs/SLOs, and incident response processes consistently across platforms — building on the platform-specific tooling in [`azure/monitoring.md`](../azure/monitoring.md), [`gcp/monitoring.md`](../gcp/monitoring.md), and [`kubernetes/observability.md`](../kubernetes/observability.md).

## Why Architects Use It
As organizations span multiple clouds/platforms, fragmented per-platform observability tooling slows incident response and hides platform-wide health — a deliberate cross-cutting strategy is needed to maintain a single, coherent operational picture.

## Architecture Considerations
- **SLIs/SLOs/error budgets as the organizing principle**: define Service Level Indicators tied to actual user experience, Service Level Objectives as the target, and use error budgets to balance reliability investment against feature velocity — this SRE-derived model gives a principled, business-aligned way to decide "how reliable is reliable enough," avoiding both under-investment and wasteful over-engineering.
- **Standardized instrumentation (OpenTelemetry)**: adopting a vendor-neutral instrumentation standard keeps observability backend choices flexible across platforms and avoids per-cloud vendor lock-in for something as foundational as telemetry.
- **Alert on symptoms (SLO burn rate), investigate with causes**: alerting should map to user-facing impact; detailed metrics/logs/traces are for root-cause investigation once an alert fires, not the primary alerting signal themselves.
- **Incident response process as part of the architecture**: runbooks, on-call rotation, and a clear escalation path are as much a part of "observability architecture" as the tooling — tooling without process doesn't actually improve incident response time.

## Common Mistakes
- Fragmented per-platform observability with no unified view, slowing cross-platform incident diagnosis.
- Alerting on raw infrastructure thresholds instead of SLO burn rate, causing alert fatigue and missed real issues.
- No error budget concept, leading to either excessive caution (over-engineering reliability at the cost of feature velocity) or excessive risk-taking (no principled stop signal when reliability degrades).

## Principal-Level Questions
- How would you design observability for a platform spanning multiple clouds and Kubernetes clusters?
- How do error budgets change the conversation between a platform team and a feature team about reliability investment?

## Scenario Question
> How would you design observability for a multi-cloud platform?

## Strong Answer (Outline)
1. Standardize instrumentation on OpenTelemetry across all services regardless of underlying cloud, so telemetry format/collection is consistent even if the storage/visualization backend differs per platform initially.
2. Define SLOs per critical user journey (not per infrastructure component), spanning whichever cloud(s) that journey actually depends on end to end.
3. Aggregate metrics/traces into a federated or centralized backend giving one queryable view across clouds (see [`kubernetes/observability.md`](../kubernetes/observability.md) for the federation pattern), while preserving platform-specific tooling for local, low-latency debugging.
4. Alert on SLO burn rate centrally, routed to the correct owning team regardless of which cloud the underlying service runs in.
5. Establish a single, cross-platform incident response runbook and on-call model, rather than separate, inconsistent processes per cloud.

## Follow-Up Questions
- How would you handle differing native tooling maturity between clouds (e.g., one cloud's native tooling being significantly more mature)?
- How do you keep observability cost proportional to value as the platform scales across clouds?

## Trade-offs
- Unified cross-cloud observability: much faster incident response and clearer platform-wide health visibility, requires deliberate standardization investment that's easy to defer in favor of "just use each cloud's native tools" — which compounds into fragmentation over time.
