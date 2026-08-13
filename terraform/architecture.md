# Terraform Architecture

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Terraform's core model: providers (plugins translating HCL into cloud API calls), resources (managed infrastructure objects), data sources (read-only references to existing infrastructure), and the plan/apply lifecycle that reconciles declared configuration against real infrastructure state.

## Why Architects Use It
Terraform is the dominant multi-cloud IaC tool because its provider model lets one consistent workflow and language target Azure, GCP, AWS, and hundreds of other systems — critical for organizations standardizing tooling across a multi-cloud or hybrid estate.

## Architecture Considerations
- **Declarative, not imperative**: you declare desired end state; Terraform computes the diff and the necessary actions — this is fundamentally different from a scripted/imperative provisioning tool and changes how you reason about idempotency.
- **Provider version pinning**: unpinned provider versions are a common source of "it worked yesterday" failures — pin explicitly and upgrade deliberately.
- **Resource graph**: Terraform builds a dependency graph from resource references, enabling safe parallel apply where possible — understanding this helps explain why certain refactors (e.g., renaming a resource) require `moved` blocks or state surgery, not just an edit.
- **Data sources for cross-stack references**: read existing infrastructure (e.g., a shared VNet) into a stack without owning it — critical for the platform/workload separation pattern used in landing zones.

## Common Mistakes
- Unpinned provider versions causing unexpected breaking changes on `terraform init`.
- Treating Terraform as imperative scripting (relying on `local-exec` provisioners heavily) instead of leaning on the declarative resource graph.
- Massive single-stack configurations covering an entire estate, making blast radius of any change enormous.
- Manual changes made outside Terraform ("ClickOps"), causing drift and eventual plan/apply surprises.

## Principal-Level Questions
- How would you structure Terraform to safely manage infrastructure across 40 subscriptions/projects with different teams owning different pieces?
- What's the risk of large, monolithic Terraform state, and how do you mitigate it?

## Scenario Question
> A single Terraform configuration manages your entire organization's Azure landing zone, and a recent `apply` accidentally modified unrelated production resources due to an unintended dependency.

## Strong Answer (Outline)
1. Root cause: a monolithic state/configuration meant an unrelated change triggered a broader diff than intended — the blast radius of any single change was the entire estate.
2. Decompose into smaller, purpose-scoped stacks (e.g., per-subscription, per-environment, or per-layer: networking, identity, workload) each with its own state file, connected via data sources or remote state references rather than one shared graph.
3. Introduce a mandatory `plan` review step in CI/CD with explicit scoped blast-radius visibility (which resources would change) before any `apply`, especially for shared/platform-layer stacks.
4. Add guardrails: require `terraform plan` diff review by a second person for platform-layer stacks, and restrict `apply` execution identity to least-privilege per stack (see [`terraform/security.md`](security.md)).

## Follow-Up Questions
- How do you handle genuine cross-stack dependencies once you've decomposed into smaller stacks?
- What's your strategy for the initial decomposition without a risky big-bang state migration?

## Trade-offs
- Monolithic state: simpler mental model initially, dangerous blast radius at scale.
- Decomposed stacks: much safer blast radius, added complexity in managing cross-stack references and apply ordering.
