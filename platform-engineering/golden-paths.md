# Golden Paths

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
A pre-approved, well-supported, opinionated path for a common workflow (provisioning a new service, deploying to production, adding a database) — designed so following it is easier than not following it, embedding security/compliance/best-practice defaults automatically.

## Why Architects Use It
Golden paths convert governance from "review and gatekeep every request" into "make the compliant option the default, easy option" — this is the practical mechanism that reconciles velocity with governance (see [`docs/principal-architect-mindset.md`](../docs/principal-architect-mindset.md) Section 4).

## Architecture Considerations
- **Opinionated but not mandatory-only**: a genuine golden path is strongly recommended and heavily supported, but a deliberate, reviewed exception process should exist for teams with real, different needs — otherwise it becomes a golden cage that drives shadow IT.
- **Backed by real tooling, not just documentation**: a golden path expressed only as a wiki page gets ignored; one expressed as a working template/module/scaffolding tool (e.g., `platform new-service`) gets adopted because it's genuinely the path of least resistance.
- **Versioned and evolvable**: golden paths should improve over time as a product, with a clear migration story for existing adopters when the path changes.
- **Guardrails embedded, not bolted on**: security/compliance defaults (network isolation, least-privilege IAM, tagging, encryption) should be baked into the path itself, backed by policy-as-code as a verification layer, not left to individual teams to remember.

## Common Mistakes
- Documentation-only golden paths that nobody actually follows because they're not the easiest option.
- No exception process, causing teams with genuine edge cases to abandon the platform entirely.
- Golden paths that go stale (built once, never updated) as underlying platform capabilities evolve.

## Principal-Level Questions
- How do you design a golden path that's genuinely easier to follow than not, rather than just documented as "recommended"?
- How do you handle a team that needs to deviate from the golden path for a legitimate reason?

## Scenario Question
> A platform golden path exists for provisioning new services, but adoption is low — most teams still provision infrastructure manually.

## Strong Answer (Outline)
1. Investigate why adoption is low — commonly the golden path is documentation-only, missing real self-service tooling, or doesn't actually cover common real-world variations teams need.
2. Rebuild the golden path as genuinely automated, self-service tooling (e.g., a CLI/portal action triggering a parameterized Terraform module + CI/CD pipeline setup) rather than a checklist to follow manually.
3. Interview a sample of teams who bypassed it to understand specific gaps, and close the highest-impact ones first.
4. Make the golden path measurably faster than the manual alternative (time-to-first-deploy) and communicate this concretely, since developer time savings drive genuine adoption better than mandates.
5. Add a lightweight, fast exception process for genuine edge cases so the golden path doesn't become an obstacle for the remaining legitimate variation.

## Follow-Up Questions
- How would you sunset an old, informally-followed pattern in favor of the new golden path without breaking existing services?
- How do you keep the golden path from becoming outdated as cloud services evolve?

## Trade-offs
- Fully automated, opinionated golden paths: high velocity and consistency, meaningful upfront and ongoing engineering investment to build and maintain the tooling itself.
