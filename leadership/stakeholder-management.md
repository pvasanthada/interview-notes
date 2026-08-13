# Stakeholder Management

**Label:** `Interview Preparation`

## Why This Matters at Principal Level
A principal architect routinely needs buy-in from people who don't report to them and don't share their technical background — security, finance, product, executive leadership — each with different priorities and vocabularies for the same underlying decision.

## Core Techniques

- **Translate technical trade-offs into the stakeholder's own terms.** A security team cares about risk reduction; finance cares about cost/ROI; product cares about velocity and customer impact. The same architectural decision should be framed differently for each audience without changing its substance.
- **Lead with the business outcome, not the technology.** "This reduces our mean-time-to-recovery from 4 hours to 30 minutes, at an additional $X/month" lands better than "we're adopting active-passive multi-region failover."
- **Bring data, not just opinion, to a disagreement.** A pilot, a benchmark, or a concrete cost estimate moves a conversation forward faster than architectural preference alone (see the worked example in [`behavioral.md`](behavioral.md)).
- **Identify the real objection, not the stated one.** Pushback framed as "this is too complex" is sometimes actually "I don't trust this team to operate it" or "I'm worried about my own team's headcount." Ask clarifying questions before responding to the surface objection.
- **Give stakeholders a genuine choice, not a fait accompli.** Present 2–3 real options with trade-offs rather than a single "take it or leave it" recommendation — this builds buy-in and often surfaces a constraint you hadn't considered.

## Common Failure Patterns
- Over-explaining technical detail to a non-technical stakeholder, losing their engagement.
- Treating disagreement as something to "win" rather than a signal that the framing hasn't landed yet.
- Waiting until a decision is finalized to loop in an affected stakeholder, creating an adversarial dynamic instead of a collaborative one.

## Principal-Level Interview Questions
- How do you communicate a technical trade-off to a non-technical executive?
- Describe a time you had to manage competing priorities between security and product velocity.

## Scenario Question
> A security team wants to block all outbound internet access from a Kubernetes cluster; the product team says this would break a critical third-party integration and delay a launch by weeks.

## Strong Answer (Outline)
1. Identify the actual underlying goals: security wants to reduce exfiltration risk; product wants to ship on time — these aren't inherently incompatible, just currently framed as a binary.
2. Propose a middle path: an explicit, reviewed allowlist for the specific third-party integration's endpoints via centralized egress firewall rules (see [`networking/routing.md`](../networking/routing.md)), rather than either "block everything" or "allow everything."
3. Bring both teams into the same conversation rather than mediating separately, so the trade-off and proposed solution are visible to both, reducing the sense that either side "lost."
4. Document the decision (a lightweight ADR, see [`architecture/governance.md`](../architecture/governance.md)) so the rationale is preserved for future review.

## Follow-Up Questions
- How would you handle a case where the two stakeholders' goals are genuinely, irreducibly in conflict?
- How do you maintain trust with a stakeholder whose request you ultimately didn't fully accommodate?
