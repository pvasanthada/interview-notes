# AI Agents

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
Systems where an LLM doesn't just generate a single response, but plans and executes a sequence of actions — calling tools/APIs, reasoning over intermediate results, and iterating — to accomplish a multi-step goal, as opposed to a single-turn prompt/response interaction.

## Why Architects Use It
Agentic patterns extend GenAI from "answer a question" to "accomplish a task," but they introduce a materially different (and higher) risk and reliability profile than single-turn generation, which architects need to design for deliberately rather than treat as a drop-in extension of existing GenAI infrastructure.

## Architecture Considerations
- **Agent vs. workflow automation**: a genuine agent dynamically decides its own sequence of actions based on model reasoning; a workflow automation follows a predetermined sequence with the model filling in specific steps — many "agent" use cases in production are actually better served, more reliably and cheaply, by a structured workflow with the model handling only the genuinely ambiguous steps (see [`comparison-tables.md`](../comparison-tables.md)).
- **Tool/action scoping**: every tool an agent can call is effectively a capability grant — apply the same least-privilege discipline as any other system identity (see [`security/iam.md`](../security/iam.md)); an agent with broad, unscoped tool access is a significant security and reliability risk (a model reasoning error becomes an unintended real-world action).
- **Human-in-the-loop for consequential actions**: irreversible or high-impact actions (financial transactions, data deletion, external communications) should require explicit human approval in the loop, not be fully autonomous, until the system has a long track record of reliability for that specific action class.
- **Observability for multi-step reasoning**: agent failures often occur mid-chain (a bad intermediate decision compounding into a wrong final action) — tracing the full reasoning/action chain, not just the final output, is essential for debugging and evaluation.
- **Bounded iteration**: agents that plan their own loops need hard limits (max steps, timeout, cost ceiling) to prevent runaway loops from an ambiguous or adversarial input.

## Common Mistakes
- Reaching for a fully autonomous agent architecture when a simpler, more reliable, and more auditable structured workflow would accomplish the same goal with far less risk.
- Granting an agent broad tool access "for flexibility" rather than the minimum set of scoped actions the use case actually requires.
- No approval gate for consequential/irreversible actions, allowing a reasoning error to cause real-world harm.
- No bounded iteration limits, risking runaway cost/loops on ambiguous inputs.

## Principal-Level Questions
- When would you choose an agentic architecture over a simpler deterministic workflow, and what would make you push back on "let's make it an agent"?
- How would you design human-in-the-loop approval for a consequential agent action without eliminating the efficiency benefit the agent was meant to provide?

## Scenario Question
> A product team wants to build an AI agent platform that can autonomously handle customer refund requests, including issuing the refund.

## Strong Answer (Outline)
1. Push back on full autonomy for the consequential action first: refund issuance is financially irreversible and should have a human approval gate, at least initially — the agent can reliably automate everything up to that point (triage, gathering order details, drafting a recommended resolution) with much lower risk.
2. Scope the agent's tool access narrowly: read access to order/customer data, and a "propose refund" action rather than an "execute refund" action, with the actual execution gated behind human approval or a separate, tightly-scoped, rules-based system for genuinely low-risk/low-value cases only.
3. Instrument full reasoning-chain observability so any incorrect recommendation can be traced back to the specific retrieval or reasoning step that produced it, supporting both debugging and a continuous evaluation loop.
4. Set bounded iteration/cost limits per request to prevent runaway loops, and define clear fallback behavior (escalate to a human) when the agent can't resolve a case confidently within those bounds.
5. Roll out incrementally — human-approves-every-action first, moving toward more autonomy for well-understood, low-risk case classes only after a demonstrated reliability track record.

## Follow-Up Questions
- How would you decide which case classes are safe to eventually fully automate?
- How do you evaluate an agent's reasoning quality, not just its final output accuracy?

## Trade-offs
- Agentic autonomy: higher potential efficiency for complex, variable tasks, meaningfully higher risk surface and harder-to-predict failure modes than a deterministic workflow — justify the added risk with a concrete efficiency case, not novelty.
