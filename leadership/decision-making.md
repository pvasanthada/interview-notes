# Decision-Making Under Ambiguity

**Label:** `Interview Preparation`

## Why This Matters at Principal Level
Principal-level decisions are frequently made with incomplete information, under time pressure, and with real consequences either way — "wait for perfect information" is often not an available option, and interviewers specifically probe how you reason and act under that constraint.

## Core Practices
- **Distinguish one-way doors from two-way doors** (see [`docs/principal-architect-mindset.md`](../docs/principal-architect-mindset.md)) — move fast and accept some risk on reversible decisions; slow down, gather more input, and de-risk before committing on irreversible ones.
- **Make your assumptions explicit and falsifiable.** State what you believe to be true and why, so if it turns out wrong, the decision can be revisited quickly rather than being defended as if it were fact.
- **Set a decision deadline, not just a decision process.** Ambiguity can expand to fill available time — a self-imposed or team deadline forces a decision to actually get made.
- **Seek the minimum information that changes the decision**, not maximum information. Ask: "what specific piece of missing information would actually change what I'd choose here?" rather than gathering broadly.
- **Communicate confidence level honestly**, not false certainty — "I'm fairly confident in this, but here's the specific thing that would make me reconsider" is a stronger, more trustworthy statement than unwarranted certainty.

## Common Failure Patterns
- Analysis paralysis — continuing to gather information well past the point of diminishing returns because uncertainty feels uncomfortable.
- False confidence — presenting a genuinely uncertain decision as if it were settled fact, which erodes trust when it later proves wrong.
- Treating every decision with the same level of rigor regardless of its actual reversibility/impact.

## Principal-Level Interview Questions
- Describe a significant decision you had to make with incomplete information. How did you approach it?
- How do you know when you have "enough" information to decide, versus needing to gather more?

## Scenario Question
> You need to recommend a database technology for a new critical system within one week, but the team's actual future scale requirements won't be fully clear for another 6 months.

## Strong Answer (Outline)
1. Classify the decision: is this a one-way or two-way door? A database choice is often more reversible early (before significant data volume/dependent logic accumulates) than commonly assumed — this affects how much certainty is actually needed now versus how much can be deferred or hedged.
2. Identify the minimum information genuinely needed: not the full 6-month forecast, but the specific access-pattern characteristics (read/write ratio, consistency needs, relational vs. document shape) that are likely knowable now even without a precise scale number.
3. Choose a solution matched to the known access pattern with reasonable headroom, explicitly favoring flexibility (e.g., a database technology with a clear scaling/migration path) over betting heavily on precise long-term scale being right.
4. Document the decision and its explicit assumptions (an ADR), including what would trigger a revisit — so if the 6-month forecast reveals the assumption was wrong, the team has a clear, pre-agreed trigger to reconsider rather than either ignoring the mismatch or panicking.

## Follow-Up Questions
- How would your approach differ if this decision were genuinely irreversible (e.g., a foundational data model choice with no realistic migration path)?
- How do you communicate this kind of provisional, assumption-based decision to a stakeholder who wants certainty?
