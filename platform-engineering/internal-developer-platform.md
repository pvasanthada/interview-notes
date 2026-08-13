# Internal Developer Platforms (IDP)

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The overarching system — combining self-service infrastructure, golden paths, a developer portal/catalog (e.g., Backstage-style service catalogs), CI/CD, and observability — that together constitutes "the platform" a developer interacts with to build, deploy, and operate a service.

## Why Architects Use It
An IDP is the concrete, product-shaped realization of platform engineering principles: it's what makes "self-service within guardrails" tangible and usable day to day, rather than a scattered collection of separately-run tools.

## Architecture Considerations
- **Service catalog as the source of truth**: a central, queryable inventory of what services exist, who owns them, their dependencies, and their operational status — critical at scale for both developers (discoverability) and platform/security teams (governance visibility).
- **Composability over a single monolithic tool**: a good IDP integrates existing best-of-breed tools (CI/CD, observability, IaC) behind a coherent developer-facing interface, rather than forcing a full replacement of an org's existing toolchain.
- **API-first**: the platform's capabilities (provisioning, catalog, deployment) should be exposed as APIs consumed by both a UI portal and CLI/automation, not built UI-first with automation as an afterthought.
- **Operating model**: the IDP itself needs an owning team, a roadmap, and SLAs — it is genuinely platform-as-product, requiring the same rigor as any customer-facing product.

## Common Mistakes
- Building or adopting an IDP tool without first establishing the underlying golden paths/self-service automation it's meant to surface — a portal in front of manual processes doesn't solve the actual problem.
- No clear service ownership data in the catalog, making it stale and untrustworthy within months.
- Treating IDP adoption as mandatory from day one across the whole org rather than proving value with an early-adopter team first.

## Principal-Level Questions
- How would you decide whether to build a custom IDP or adopt an existing platform (e.g., Backstage) as the foundation?
- How do you keep a service catalog from becoming stale/inaccurate over time?

## Scenario Question
> Leadership wants a unified developer portal so engineers can discover services, request infrastructure, and view deployment status in one place, replacing a patchwork of tools.

## Strong Answer (Outline)
1. Before building a portal, confirm the underlying capabilities (self-service provisioning, golden paths, CI/CD, observability) actually exist and work well independently — a portal is a UI layer over these, not a substitute for them.
2. Evaluate build-vs-adopt: an established open-source foundation (e.g., Backstage) is usually preferable to a fully custom build, given the ecosystem of existing plugins for catalog, CI/CD, and docs integration — reserve custom build for genuinely unmet needs.
3. Populate the catalog by integrating with existing systems of record (Git repo metadata, CI/CD, cloud resource tags) rather than requiring manual entry, to keep it accurate over time.
4. Roll out with an early-adopter team, iterate based on real usage feedback, then expand — avoid a big-bang mandatory rollout before the platform has proven value.
5. Assign clear, ongoing ownership (a platform team) with a defined roadmap and support model.

## Follow-Up Questions
- How would you keep catalog data accurate as services are added, deprecated, or change ownership?
- How do you measure whether the IDP investment is paying off?

## Trade-offs
- Adopting an established open-source IDP foundation: faster time-to-value, ecosystem support; may require adapting some organizational workflows to fit the tool's model rather than the reverse.
