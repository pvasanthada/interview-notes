# Self-Service Infrastructure

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The capability for application teams to provision compliant infrastructure (compute, networking baseline, databases, CI/CD pipelines) without requiring a manual request to, or direct privileged access from, a central platform/infrastructure team.

## Why Architects Use It
Removes the central platform team as a bottleneck for the common case while avoiding the alternative of granting broad standing access to application teams — self-service *within guardrails* is how governance and velocity coexist at scale.

## Architecture Considerations
- **Self-service interface, not direct cloud access**: teams interact through a portal/CLI/pipeline (backed by IaC modules), not direct subscription/project access — the automation runs with appropriately scoped privilege, the human requester doesn't need broad standing access themselves.
- **Policy-as-code as the enforcement backstop**: self-service requests still pass through automated guardrail checks (see [`terraform/policy-as-code.md`](../terraform/policy-as-code.md)) before provisioning, so self-service doesn't mean ungoverned.
- **Progressive trust model**: start with a curated, limited set of self-service options (the golden path); expand the catalog over time as confidence and tooling maturity grow, rather than attempting to cover every possible request from day one.
- **Approval-free for the common case, fast-tracked review for edge cases**: most requests should require zero manual approval; genuine exceptions get a fast, well-defined review path rather than being blocked entirely.

## Common Mistakes
- "Self-service" that's actually a ticket queue with a form — no real reduction in lead time, just a different intake mechanism.
- Granting broad standing cloud access to enable self-service, defeating the least-privilege goal self-service was meant to preserve.
- No policy-as-code backstop, so a bug in the self-service tooling itself could provision non-compliant infrastructure at scale.

## Principal-Level Questions
- Design a self-service cloud platform that allows developers to provision compliant infrastructure without requiring direct access to cloud subscriptions/projects.
- How do you decide what's safe to fully self-service versus what still needs a manual review step?

## Scenario Question
> Design a self-service cloud platform that allows developers to provision compliant infrastructure without requiring direct access to cloud subscriptions/projects.

## Strong Answer (Outline)
1. Requester interface: a service catalog (portal or CLI) exposing a curated set of pre-approved infrastructure patterns (e.g., "new microservice," "new managed database") as parameterized requests, not free-form cloud resource creation.
2. Backend execution: requests trigger a CI/CD pipeline running Terraform with a scoped, federated execution identity (per [`terraform/security.md`](../terraform/security.md)) — the requester never receives direct subscription/project credentials.
3. Guardrails: policy-as-code validation runs as part of the pipeline (network isolation, tagging, encryption defaults) before any resource is created — a request that fails policy is rejected automatically with a clear reason, not silently blocked.
4. Governance: an audit trail of every request (who, what, when, approved-automatically vs. reviewed) feeding both security review and platform usage analytics.
5. Exception path: requests outside the curated catalog route to a lightweight manual review (platform team), tracked and used to inform future catalog expansion.

## Follow-Up Questions
- How would you extend this to support genuinely novel infrastructure needs the catalog doesn't yet cover?
- How do you handle decommissioning/cleanup of self-service-provisioned resources that are no longer needed?

## Trade-offs
- Curated self-service catalog: fast, safe, consistent for the common case; requires ongoing investment to expand coverage as legitimate new needs emerge, or teams hit the "not supported yet" wall too often.
