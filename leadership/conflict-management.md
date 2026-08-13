# Conflict Management

**Label:** `Interview Preparation`

## Why This Matters
Principal architects regularly sit at the intersection of disagreement — between teams, between security and velocity, between two valid but incompatible technical approaches. How you handle that disagreement is often more visible (and more interview-relevant) than the specific technical decision itself.

## Core Techniques
- **Separate the person from the position.** Disagree with an architecture, not with the person proposing it — and say so explicitly if tension is rising ("I think this approach has a real gap, not that you did anything wrong here").
- **Seek the shared goal underneath the disagreement.** Two engineers arguing about microservices vs. monolith are usually both optimizing for something legitimate (velocity vs. operational simplicity) — surfacing the shared goal ("we both want this to ship reliably and be maintainable") reframes the conversation from adversarial to collaborative.
- **Use data and pilots to de-escalate opinion-based conflict.** A disagreement based on untested opinion can often be resolved (or at least de-escalated) by a small, time-boxed experiment rather than continued debate.
- **Know when to make the call and move on.** Not every disagreement needs full consensus — as the architect, sometimes the right move is to hear both sides genuinely, make a decision, document the reasoning, and hold the line so the team can move forward.
- **Address conflict directly and promptly**, not by avoidance — unaddressed technical disagreements tend to resurface repeatedly and erode trust over time.

## Common Failure Patterns
- Avoiding a needed disagreement to preserve short-term harmony, letting a flawed architecture proceed.
- Winning an argument in a way that damages the relationship, making future collaboration harder.
- Re-litigating settled decisions repeatedly because the original disagreement wasn't actually resolved, just paused.

## Principal-Level Interview Questions
- Describe a time you had a significant technical disagreement with a peer or your manager. How did you resolve it?
- How do you decide when to escalate a disagreement versus resolve it at your own level?

## Scenario Question
> Two senior engineers on your team have been in an unresolved disagreement for weeks about whether to adopt a service mesh, and it's starting to affect team morale and blocking a roadmap item.

## Strong Answer (Outline)
1. Bring both engineers together (not separately) to make the disagreement explicit and shared, rather than letting it continue as parallel private frustration.
2. Reframe around the underlying requirement: ask what specific problem the service mesh is meant to solve (e.g., mTLS enforcement, fine-grained traffic control) and evaluate whether that specific need genuinely requires a mesh or could be met more simply (per the trade-off framing in [`kubernetes/networking.md`](../kubernetes/networking.md)).
3. If the disagreement is genuinely opinion-based rather than fact-based, propose a small, time-boxed pilot on a non-critical service to gather real data rather than continuing to debate hypothetically.
4. If a decision needs to be made without full consensus (roadmap pressure), make the call, document the reasoning in an ADR, and be explicit that the team is now aligned and moving forward together, addressing any residual tension directly rather than letting it linger.

## Follow-Up Questions
- How would you handle it if the pilot data was genuinely ambiguous and didn't clearly favor either side?
- How do you rebuild trust with an engineer whose position you ultimately didn't choose?
