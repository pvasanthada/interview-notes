# Terraform Modules

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Reusable, versioned, parameterized Terraform configurations that encapsulate a pattern (e.g., "a compliant Azure Storage Account" or "an AKS cluster with baseline security config") so it can be consumed consistently across many stacks/teams.

## Why Architects Use It
Modules are the primary mechanism for turning platform standards (security baselines, naming conventions, tagging) into something enforced by default rather than by documentation and hope — the technical foundation of a golden path (see [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md)).

## Architecture Considerations
- **Module versioning**: publish modules to a registry (Terraform Cloud/Enterprise registry, or a Git-tag-based source) with semantic versioning; consumers pin to a version range deliberately, not to `main`/latest.
- **Module scope**: a good module encapsulates one clear concern with sensible defaults and a small number of meaningful inputs — avoid "god modules" that try to parameterize every possible variation, which become as hard to reason about as no module at all.
- **Composability**: prefer smaller, composable modules (network module + compute module used together) over one giant module per "application," which tends to duplicate logic and constrain flexibility.
- **Testing**: modules should have automated tests (e.g., Terratest, or native `terraform test`) validating they produce the expected plan/output, especially for security-relevant defaults.

## Common Mistakes
- No versioning discipline — every consumer points at a module's `main` branch, so an unrelated change breaks everyone simultaneously.
- Modules with dozens of optional inputs trying to cover every edge case, becoming as complex as raw resource blocks.
- No tests, so a module's security defaults can silently regress.
- Publishing modules without documentation, making adoption slower than just writing raw resources.

## Principal-Level Questions
- How would you design a module strategy that enforces security baselines without becoming an adoption bottleneck?
- How do you handle a breaking change in a widely-used module across 50 consuming stacks?

## Scenario Question
> Your organization has 50 Terraform stacks each implementing storage accounts slightly differently, several missing required encryption/network settings. Standardize this.

## Strong Answer (Outline)
1. Build a versioned `storage-account` module encoding the required baseline (private network access, encryption, diagnostic settings) as defaults, with a small set of intentional variation points (SKU, redundancy tier).
2. Publish to an internal registry with semantic versioning starting at 1.0.0.
3. Migrate consuming stacks incrementally — this is a refactor, not a rewrite, using `terraform state mv`/`moved` blocks where resource addresses change, validated via `plan` showing no unintended destroy/recreate.
4. Add a policy-as-code check (see [`policy-as-code.md`](policy-as-code.md)) as a backstop catching any stack that bypasses the module and creates a non-compliant storage account directly.
5. Communicate the migration deadline and provide a clear upgrade guide/changelog per module version.

## Follow-Up Questions
- How would you handle a team that has a genuine need to deviate from the module's defaults?
- How do you prevent modules from becoming too rigid to evolve as requirements change?

## Trade-offs
- Highly opinionated modules: strong consistency and compliance-by-default, less flexibility for genuine edge cases — needs a clear escape hatch/exception process.
