# Architecture Governance

**Label:** `Reference Architecture`

## What It Is
The organizational processes and artifacts (architecture review boards, Architecture Decision Records, standards/reference architectures) that ensure architectural decisions are made consistently, are documented, and align with organizational strategy — distinct from cloud governance (Policy/Org Policy enforcement, covered in [`azure/governance-policy.md`](../azure/governance-policy.md) and [`gcp/governance.md`](../gcp/governance.md)), though the two are complementary.

## Why Architects Use It
Without deliberate governance, architecture decisions accumulate inconsistently across teams — duplicated effort, incompatible patterns, and undocumented rationale that makes future changes riskier because nobody remembers why something was built a certain way.

## Architecture Considerations
- **Architecture Decision Records (ADRs)**: lightweight, versioned documents capturing context, options considered, the decision, and consequences for significant architectural choices — see [`adr/`](../adr/) at the repo root for worked examples. ADRs create institutional memory that survives team turnover.
- **Architecture review process, scaled to decision impact**: not every decision needs a formal review — reserve review boards/committees for genuinely high-impact, cross-cutting, or expensive-to-reverse decisions (one-way doors, per [`docs/principal-architect-mindset.md`](../docs/principal-architect-mindset.md)), and let teams decide two-way-door decisions autonomously within established standards.
- **Reference architectures over rigid mandates**: publish well-reasoned reference architectures (recommended default patterns) rather than rigid, exception-free mandates — pairs with the golden-path philosophy in [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md).
- **Governance as enablement, not just gatekeeping**: the goal of architecture governance is faster, more consistent decision-making across the organization, not slower decision-making via committee — measure and manage against that goal explicitly.

## Common Mistakes
- Architecture review boards that become a bottleneck reviewing every decision regardless of impact, slowing the organization without proportional risk reduction.
- No ADRs, so architectural rationale is lost when team members leave, and later teams re-litigate settled decisions or accidentally violate constraints nobody remembers.
- Governance processes with no clear escalation/exception path, driving teams to quietly bypass them.

## Principal-Level Questions
- How would you design an architecture governance process that scales decision-making rigor to actual decision impact, rather than treating everything uniformly?
- How do you keep ADRs a living practice rather than a one-time documentation exercise that fades out?

## Scenario Question
> Your architecture review board has become a bottleneck — every infrastructure change, regardless of scale, requires a 2-week review cycle, and teams are frustrated.

## Strong Answer (Outline)
1. Diagnose: uniform review rigor applied to decisions of vastly different impact/reversibility — this is a mismatch, not an inherent flaw in having governance at all.
2. Redesign: classify decisions by impact/reversibility (a lightweight rubric — e.g., cost threshold, cross-team impact, reversibility) and route only genuinely high-impact/one-way-door decisions to full board review; two-way-door and low-impact decisions proceed autonomously within published standards/golden paths, optionally with lightweight async notification rather than a full review cycle.
3. Convert the review board's accumulated knowledge into published reference architectures/ADRs so common patterns don't need repeated review at all — the board's time shifts from reviewing routine requests to maintaining these standards and handling genuine exceptions.
4. Track and report cycle-time improvement to demonstrate the redesign's impact, and keep the classification rubric itself under periodic review as the organization's risk tolerance and platform maturity evolve.

## Follow-Up Questions
- How would you handle a decision that's ambiguous under your impact-classification rubric?
- How do you prevent the lightweight-review path from becoming a way to quietly avoid necessary scrutiny on a genuinely risky decision?

## Trade-offs
- Rigor-proportional-to-impact governance: much faster overall organizational velocity, requires a well-calibrated classification process or risk under-reviewing something that should have gotten more scrutiny.
