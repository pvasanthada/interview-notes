# Quick Revision — 30–60 Minutes Before an Interview

**Label:** `Interview Preparation`

Concise reminders only. For depth, go back to the domain folders. Read this once, calmly, and stop.

## Mindset (read first)
> Engineer answer vs. Principal Architect answer: always start from requirements/constraints, state assumptions, offer alternatives, name trade-offs, mention cost and operational impact, close with what you'd validate. ([`docs/principal-architect-mindset.md`](docs/principal-architect-mindset.md))

## Frameworks
- **Technical**: Requirements → Assumptions → Constraints → Architecture → Alternatives → Trade-offs → Security → Scale → Availability → Observability → Cost → Implementation → Operations
- **Scenario**: Context → Problem → Analysis → Options → Decision → Trade-offs → Implementation → Outcome → Lessons
- **Architecture**: Requirements → Constraints → Architecture → Data Flow → Security → Reliability → Scalability → Operations → Cost → Trade-offs
- **Behavioral**: STAR (+ Reflection)

## Azure
- Landing zone = management groups → subscriptions → resources, audit-then-enforce policy.
- Identity: least privilege, managed identity over secrets, PIM for all standing privileged roles.
- Networking: hub-spoke or Virtual WAN, private endpoints + private DNS, centralized egress.
- DR: RTO/RPO drives the pattern — active-active only if truly justified.

## GCP
- Hierarchy: Org → Folders → Projects (many small projects is the GCP-native pattern).
- Global VPC (one VPC can span regions) — different from Azure's regional VNet model.
- IAM: predefined roles over primitive roles; Workload Identity Federation eliminates service account keys.
- VPC Service Controls: defense against exfiltration even with valid IAM.

## Kubernetes
- Control plane: API server → etcd (source of truth) → scheduler/controller-manager reconciliation loop.
- Networking: default-deny NetworkPolicy, Ingress/Gateway API, service mesh only with a real driving requirement.
- Autoscaling: HPA on the right metric (not always CPU), respect PDB vs. Cluster Autoscaler interaction.
- Troubleshoot layer by layer: events → logs → recent changes → isolate scope.

## Terraform
- Workspaces = near-identical envs, same team; separate state/config = different config or access control needed.
- Plan-on-PR, apply-on-merge with manual gate for prod; least-privilege federated execution identity.
- Policy-as-code (plan-time) + cloud policy (deployed-resource) = two layers, not either/or.
- Drift: investigate before reconciling — might be a legitimate emergency change.

## Networking
- OSI/TCP-IP: isolate the layer before diagnosing.
- DNS is disproportionately the root cause of "connectivity" issues — check private DNS zone linkage first.
- L4 vs L7 load balancing; deep health checks over shallow ones.
- Hybrid: dedicated circuit + VPN failover, never single point of failure.

## Security
- Zero Trust: explicit verification, least privilege, assume breach — identity first, then device, then network.
- Secrets: eliminate via workload identity before reaching for "store it better."
- CSPM: secure score is a leading indicator; triage by exploitability/blast radius, not raw count.

## Platform Engineering
- Golden paths: automated self-service, not documentation; guardrails embedded, exception process exists.
- Measure DX with DORA metrics + time-to-first-deploy, not vibes.
- Self-service ≠ broad standing access — scoped execution identity + policy-as-code backstop.

## AI/GenAI — always state maturity label first
- RAG: chunking strategy and retrieval-time access control matter more than model choice.
- Agents: least-privilege tool scoping, human-in-the-loop for consequential actions, bounded iteration.
- MCP: standardizes integration, does not inherently secure it — same least-privilege discipline applies.
- Cost: cache, route by complexity to cheaper models, track token usage per feature.

## Architecture
- Monolith default until proven need for microservices; SQL default until proven need for NoSQL.
- HA ≠ DR (zone failure vs. region failure) — don't conflate them.
- FinOps: tagging first, then rightsize/idle-cleanup, then reserved capacity for steady-state only.

## Leadership
- Have 3 STAR stories ready: a disagreement, an incident, a time you were wrong.
- Lead with business outcome when talking to non-technical stakeholders.
- Own mistakes without over-apologizing; show what changed afterward.

## Closing move for any design question
State a one-sentence summary, name the biggest trade-off you accepted, and say what you'd want to validate next.
