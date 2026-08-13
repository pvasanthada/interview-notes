# Technical Leadership

**Label:** `Interview Preparation`

## What It Means at Principal Level
Technical leadership isn't about being the most senior individual contributor in the room — it's about setting technical direction that many teams can execute against consistently, and creating the conditions (standards, golden paths, mentorship) that let others make good decisions without you in every conversation.

## Core Practices
- **Set direction through artifacts, not just conversations.** ADRs, reference architectures, and golden paths (see [`architecture/governance.md`](../architecture/governance.md), [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md)) scale your judgment far beyond what you could achieve through 1:1 influence alone.
- **Multiply through others.** A principal architect's highest-leverage activity is often growing other engineers' judgment (see [`mentoring.md`](mentoring.md)), not personally reviewing every decision.
- **Balance opinion with humility.** Have a clear point of view and defend it, but change your mind visibly and quickly when presented with better evidence — this models the behavior you want from the teams you influence.
- **Know when to be prescriptive versus when to let teams decide.** Reserve strong, non-negotiable direction for genuine one-way-door, high-blast-radius decisions; be a consultative advisor, not a gatekeeper, for everything else.

## Common Failure Patterns
- Trying to personally review/approve too many decisions, becoming a bottleneck and signaling low trust in the teams around you.
- Setting direction only verbally, so it doesn't survive beyond the room it was discussed in.
- Being unwilling to publicly change a prior technical position, undermining trust when new information genuinely warrants a change.

## Principal-Level Interview Questions
- How do you scale your technical judgment across an organization larger than you can personally review?
- Describe a time you had to change a previously-stated technical position based on new information.

## Scenario Question
> You've noticed several teams independently reinventing the same authentication integration pattern, each with slightly different (and sometimes weaker) security defaults.

## Strong Answer (Outline)
1. Recognize this as a symptom of missing shared direction, not individual team failure — teams filled a gap because no clear standard existed.
2. Author a reference implementation/module (see [`terraform/modules.md`](../terraform/modules.md)) encoding the correct, secure default pattern, and document the rationale via an ADR.
3. Don't mandate a disruptive mass migration immediately — communicate the new standard, make adoption easy (golden path), and prioritize migrating the weakest existing implementations first based on actual risk.
4. Establish a lightweight review trigger (e.g., a policy-as-code check) so future teams default to the shared pattern rather than reinventing it, closing the loop structurally rather than relying on ongoing manual vigilance.

## Follow-Up Questions
- How would you handle a team that had already invested significant effort in their own implementation and resists migrating?
- How do you decide when a pattern is common enough across teams to warrant a formal shared standard versus leaving it to individual team discretion?
