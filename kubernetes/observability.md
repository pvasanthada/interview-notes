# Kubernetes Observability

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Prometheus (metrics collection/storage, pull-based), Grafana (visualization), and OpenTelemetry (vendor-neutral instrumentation for metrics/logs/traces) as the dominant open-source Kubernetes observability stack.

## Why Architects Use It
Kubernetes' dynamic, ephemeral nature (pods rescheduling, scaling, IPs changing constantly) makes traditional host-based monitoring approaches inadequate — this stack is purpose-built for that dynamism (service discovery via Kubernetes API, label-based querying).

## Architecture Considerations
- **Prometheus scaling**: a single Prometheus instance has practical limits at large scale — patterns like Thanos or Mimir provide long-term storage and horizontal query federation across many clusters.
- **OpenTelemetry as the instrumentation standard**: instrumenting applications with OTel (rather than a vendor-specific SDK) keeps the backend (Prometheus, a commercial APM, cloud-native tools) swappable — a meaningful architectural decision for multi-cloud or vendor-flexibility requirements.
- **RED/USE methods**: RED (Rate, Errors, Duration) for request-driven services; USE (Utilization, Saturation, Errors) for resources — a useful mental model for deciding what to actually dashboard/alert on instead of monitoring everything indiscriminately.
- **Alerting on symptoms, not causes**: alert on SLO burn rate / user-facing symptoms; use detailed metrics for root-cause investigation, not as the primary alert trigger.

## Common Mistakes
- Single Prometheus instance with no long-term storage strategy, losing historical data and hitting scaling limits.
- Instrumenting with a vendor-specific SDK, creating lock-in that becomes costly to unwind later.
- Alert rules copied from generic templates without tuning to the actual workload's normal behavior, producing constant noise.
- No correlation between metrics, logs, and traces (different tools, no shared identifiers), slowing incident diagnosis.

## Principal-Level Questions
- How would you design observability for a platform running 20 Kubernetes clusters across 2 clouds?
- Why might you choose OpenTelemetry over a vendor-specific instrumentation SDK, and what's the trade-off?

## Scenario Question
> Your platform has grown to 20 clusters across Azure and GCP. Each cluster has its own Prometheus/Grafana, and there's no unified view of platform health.

## Strong Answer (Outline)
1. Standardize instrumentation on OpenTelemetry across all workloads so the same telemetry can flow to a shared backend regardless of cluster/cloud.
2. Introduce a federated long-term storage layer (e.g., Thanos/Mimir, or a managed equivalent) that aggregates metrics from all 20 clusters' Prometheus instances into one queryable global view.
3. Centralize log/trace aggregation similarly, with consistent correlation IDs across services regardless of which cluster they run in.
4. Build a small set of platform-wide SLO dashboards on top of the federated data, while leaving per-cluster Prometheus for local, low-latency debugging.

## Follow-Up Questions
- How would you handle data sovereignty requirements that prevent some clusters' telemetry from being centrally aggregated?
- What's your approach to keeping observability infrastructure cost proportional to its value as the platform scales?

## Trade-offs
- Federated central observability: unified visibility, added infrastructure and potential single-point-of-failure risk that must itself be made resilient.
