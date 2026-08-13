# Interview Answer Frameworks

**Label:** `Interview Preparation`

Reusable structures for answering senior/principal-level interview questions. Practice these out loud — the structure should become automatic, not something you visibly count through in the interview.

---

## 1. Technical Question Framework

Use for deep-dive technical questions ("How does X work," "How would you configure Y").

1. **Clarify requirements** — what is actually being asked, and in what context?
2. **Establish assumptions** — state them explicitly so the interviewer can correct you early.
3. **Identify constraints** — compliance, budget, team skill, existing platform choices.
4. **Explain architecture** — the actual design or mechanism.
5. **Discuss alternatives** — at least one other viable approach.
6. **Explain trade-offs** — why this approach over the alternative, and what you give up.
7. **Address security** — identity, network, data protection implications.
8. **Address scalability** — how it behaves as load grows.
9. **Address availability** — failure modes and resilience.
10. **Address observability** — how you'd know it's working/broken.
11. **Address cost** — direct and operational cost.
12. **Explain implementation** — how this actually gets built (IaC, CI/CD, rollout).
13. **Explain operational model** — who owns it, how it's supported, what changes over time.

> You don't hit all 13 points for every question — use judgment on depth. For a 5-minute question, hit 4–6 of the most relevant points explicitly and mention the rest in one line.

---

## 2. Scenario Framework

Use for "tell me about a time," incident, or "what would you do if" prompts.

**Context → Problem → Analysis → Options → Decision → Trade-offs → Implementation → Outcome → Lessons Learned**

- **Context** — the environment, scale, and constraints.
- **Problem** — the specific issue, stated precisely.
- **Analysis** — how you diagnosed root cause or scoped the problem.
- **Options** — the realistic choices considered.
- **Decision** — what you chose and why.
- **Trade-offs** — what you knowingly gave up.
- **Implementation** — how it was executed, including sequencing and risk mitigation.
- **Outcome** — measurable result.
- **Lessons Learned** — what you'd do differently, or what became a standard afterward.

---

## 3. Architecture Question Framework

Use for "design a system that..." prompts.

**Requirements → Constraints → Architecture → Data Flow → Security → Reliability → Scalability → Operations → Cost → Trade-offs**

1. **Requirements** — functional and non-functional (SLA, RTO/RPO, throughput, compliance).
2. **Constraints** — existing platform, budget, timeline, team skills, regulatory.
3. **Architecture** — the high-level design, drawn/described top to bottom.
4. **Data Flow** — how data moves, is stored, and is protected in transit/at rest.
5. **Security** — identity, network segmentation, secrets, least privilege.
6. **Reliability** — failure domains, redundancy, degradation behavior.
7. **Scalability** — horizontal/vertical scaling strategy and bottlenecks.
8. **Operations** — deployment model, observability, on-call/support model.
9. **Cost** — rough cost drivers and optimization levers.
10. **Trade-offs** — what was consciously not chosen, and why.

---

## 4. STAR (Behavioral)

**Situation → Task → Action → Result**

- Keep Situation/Task brief (20% of the answer combined).
- Spend most of the time on **Action** — what *you* specifically did, not what "the team" did.
- Quantify Result wherever possible.
- Add a short **Reflection** at the end for principal-level polish: what you learned, or what changed as a standard/process afterward.

**When NOT to use STAR rigidly:** for open-ended reflective questions ("how do you think about technical debt," "how do you build trust with a new team"), a rigid STAR structure feels stilted. Use STAR for concrete "tell me about a time" prompts; use a point-of-view + example structure for philosophy/approach questions.

See [`leadership/behavioral.md`](../leadership/behavioral.md) for worked examples.

---

## 5. Quick Selection Guide

| Question phrasing | Framework |
|---|---|
| "How does...", "How would you configure..." | Technical |
| "Tell me about a time...", "What would you do if..." | Scenario or STAR |
| "Design a system that...", "Design a platform for..." | Architecture |
| "Describe a conflict...", "Tell me about a failure..." | STAR |
