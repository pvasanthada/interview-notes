# Azure Monitoring

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
Azure Monitor (metrics, alerts, autoscale trigger source), Log Analytics (centralized log platform, KQL query engine), and Application Insights (APM/tracing) — the observability stack for Azure-native and hybrid workloads.

## Why Architects Use It
Provides the operational visibility required to run production systems responsibly: detecting issues before customers do, understanding root cause quickly, and driving SLO-based decision-making.

## Architecture Considerations
- **Centralized Log Analytics workspace(s)**: typically one central workspace for platform/security logs, with a strategy for per-team/per-environment workspaces balancing centralization (easier cross-cutting queries) against cost/data-sovereignty isolation needs.
- **Metrics vs. logs vs. traces**: metrics for real-time alerting/dashboards (cheap, low-cardinality); logs for detailed investigation (expensive at scale, high-cardinality); traces for distributed request flow (essential for microservices debugging).
- **Alerting strategy**: alert on symptoms that map to user/business impact (SLO burn rate) rather than every possible metric threshold, to avoid alert fatigue.
- **Retention and cost**: Log Analytics ingestion and retention cost scales with volume — apply table-level retention tuning and consider Basic Logs tier for high-volume, low-query-value data.

## Common Mistakes
- Alerting on every infrastructure metric without regard to actual user impact, causing alert fatigue and ignored pages.
- No distributed tracing across microservices, making root-cause analysis in production incidents slow and speculative.
- Uncontrolled log ingestion volume driving runaway Log Analytics cost.

## Principal-Level Questions
- How would you design observability for a platform spanning 50+ microservices across Azure and on-prem?
- How do you decide what should page someone at 3am vs. what should just be a dashboard?

## Scenario Question
> Incident response has been slow because engineers can't quickly correlate a customer-facing error with the responsible downstream service. Improve this.

## Strong Answer (Outline)
1. Implement distributed tracing (OpenTelemetry → Application Insights/Azure Monitor) across all services with consistent correlation IDs propagated end-to-end.
2. Define SLOs per critical user journey and instrument SLI metrics directly, not just infrastructure metrics.
3. Build a small number of high-signal dashboards mapped to those user journeys, not a dashboard per microservice.
4. Alert on SLO burn-rate, not raw thresholds, and route alerts to the owning team directly via on-call rotation.

## Follow-Up Questions
- How would you handle observability cost as the platform scales to 10x current volume?
- How do you avoid tracing becoming a performance overhead itself?

## Trade-offs
- Full tracing everywhere: excellent debuggability, real cost and slight latency overhead — often sampled in high-volume production paths.
- Centralized workspace: simpler cross-cutting queries, potential cost/governance friction across teams with different retention needs.
