# Platform Engineering — Architecture

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The discipline of building and operating an internal platform — a curated set of tools, golden paths, and self-service capabilities — treated as a product with application teams as its customers, rather than a loose collection of infrastructure and ad hoc tooling.

## Why Architects Use It
As organizations scale past a handful of teams, either every team reinvents infrastructure/CI-CD/observability independently (slow, inconsistent, insecure) or a central team becomes a manual gatekeeping bottleneck. Platform engineering resolves this tension by making the compliant, best-practice path the *easiest* path, self-service, without needing a human in the loop for the common case.

## Architecture Considerations
- **Platform as a product**: has a roadmap, a feedback loop with its "customers" (application teams), and is measured by adoption and developer satisfaction, not just uptime.
- **Golden paths, not golden cages**: the platform should make the recommended path (a curated set of technology choices, templates, CI/CD) dramatically easier than going off-path, while still allowing genuine exceptions for teams with real different needs — see [`golden-paths.md`](golden-paths.md).
- **Layered abstraction**: infrastructure (cloud, Kubernetes) → platform capabilities (CI/CD, observability, secrets) → developer-facing self-service interface (portal, CLI, templates) — each layer should be independently evolvable.
- **Team topology**: platform teams are typically structured as "platform as a product" per Team Topologies — a small number of enabling/platform teams serving many stream-aligned (application) teams via well-defined, self-service interfaces (APIs, templates), not tickets.

## Common Mistakes
- Building a platform nobody asked for, disconnected from actual application-team pain points.
- Overly rigid golden paths with no exception process, driving teams to bypass the platform entirely.
- Measuring platform success by infrastructure metrics (uptime) rather than developer outcomes (time-to-first-deploy, deployment frequency, developer satisfaction).
- Central platform team becoming a manual approval bottleneck rather than building genuine self-service.

## Principal-Level Questions
- How do you decide what belongs in the platform (centrally owned) versus what application teams should own themselves?
- How would you measure whether your platform investment is actually succeeding?

## Scenario Question
> Application teams are frustrated that every new service requires 6 weeks of back-and-forth with the platform team to get networking, IAM, and CI/CD set up.

## Strong Answer (Outline)
1. Diagnose the actual bottleneck: usually a manual, ticket-based provisioning process rather than self-service — the platform team is acting as a gatekeeper, not enabling self-service.
2. Design a golden path: a templated, self-service path (e.g., a service catalog entry or Terraform module + CI/CD template) that provisions the standard 90% case (networking, IAM baseline, CI/CD pipeline) automatically within guardrails, with no manual platform-team involvement for the common case.
3. Reserve manual review only for genuine exceptions (non-standard requirements), tracked and time-bound.
4. Measure and report time-to-first-deploy before/after as the concrete success metric, driving continued platform investment.
5. Establish a feedback loop (regular check-ins with application teams) treating the platform as a living product, not a one-time project.

## Follow-Up Questions
- How would you handle a team whose workload genuinely doesn't fit the golden path?
- How do you fund and staff a platform team's ongoing roadmap versus one-off requests?

## Trade-offs
- Strong golden paths: much faster, more consistent, more secure by default; requires ongoing investment to keep paths current and cover genuine edge cases, or adoption erodes.
