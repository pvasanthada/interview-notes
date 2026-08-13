# GCP Monitoring

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
Cloud Logging (centralized log platform) and Cloud Monitoring (metrics, dashboards, alerting, uptime checks) — GCP's counterpart to Azure Monitor/Log Analytics, built on the same underlying philosophy of unifying platform and application telemetry.

## Why Architects Use It
Same operational necessity as Azure monitoring — visibility into system health, with GCP-native integration into BigQuery for advanced log analytics at scale being a notable differentiator.

## Architecture Considerations
- **Log sinks and routing**: route logs to different destinations by type/severity — Cloud Logging default bucket for short-term operational use, BigQuery export for long-term analytical querying, Cloud Storage for cheap long-term retention/compliance.
- **Log-based metrics**: derive custom metrics directly from log content for cases metrics-at-source doesn't cover.
- **SLOs in Cloud Monitoring**: first-class SLO objects with error-budget tracking, directly supporting SRE-style burn-rate alerting.
- **Multi-project observability**: use a centralized monitoring/logging project with metrics/logs scoped or aggregated from workload projects, rather than siloed per-project dashboards.

## Common Mistakes
- Leaving all logs in the default Cloud Logging bucket indefinitely, driving unnecessary cost.
- Not using SLO objects, resorting to ad hoc threshold alerts that don't map to actual user experience.
- Siloed per-project monitoring with no aggregated view for platform-wide health.

## Principal-Level Questions
- How would you design observability for a multi-cloud platform spanning GCP and Azure?
- How do you use log-based metrics vs. instrumented application metrics, and when does each make sense?

## Scenario Question
> Leadership wants a single, reliable view of platform health across 30 GCP projects with minimal noise.

## Strong Answer (Outline)
1. Centralized monitoring project with metrics scopes aggregating the 30 workload projects.
2. Define SLOs per critical service, expressed as Cloud Monitoring SLO objects, with burn-rate alerting policies (fast burn pages on-call, slow burn creates a ticket).
3. Route routine logs to Cloud Storage (cheap, compliance retention) and only operationally relevant logs to the actively-queried Cloud Logging bucket.
4. Build a small number of executive/platform-health dashboards from SLO data, not raw infrastructure metrics.

## Follow-Up Questions
- How would you unify this view with an Azure Monitor-based estate in a multi-cloud org?
- How do you keep log export costs under control as log volume grows?

## Trade-offs
- Centralized aggregation: single pane of glass, requires careful IAM design so teams see only their own detailed data while leadership sees aggregate health.
