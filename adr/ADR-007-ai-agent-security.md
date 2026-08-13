# ADR-007 — AI Agent Security

**Status**: Accepted (Reference Architecture — see maturity note below)

**Maturity note**: This ADR documents a `Reference Architecture` design decision for interview-preparation purposes. See [`ai-genai/README.md`](../ai-genai/README.md).

## Context
A team proposes building an AI agent capable of performing multi-step account management actions (e.g., updating customer records, initiating service changes) on behalf of support staff.

## Problem
Define the security architecture governing what the agent is allowed to do autonomously versus what requires human confirmation, given the consequential nature of some possible actions.

## Options
1. **Full autonomy** — fastest for the support team, unacceptable risk given the agent could take an irreversible incorrect action from a reasoning error or prompt injection (see [`ai-genai/ai-security.md`](../ai-genai/ai-security.md)).
2. **No autonomy — agent only drafts recommendations, a human executes every action manually** — safest, minimal efficiency gain over the current manual process.
3. **Tiered autonomy by action risk class** (recommended) — the agent autonomously handles low-risk, easily-reversible actions and drafts recommendations requiring human approval for higher-risk/irreversible actions.

## Decision
Adopt tiered autonomy: classify possible agent actions into risk tiers, with autonomous execution only for the lowest-risk, reversible tier, and mandatory human approval for anything consequential or irreversible.

## Rationale
- Full autonomy is not justified given the demonstrated risk of prompt injection and reasoning errors in current-generation agent systems (see [`ai-genai/ai-agents.md`](../ai-genai/ai-agents.md)).
- A fully manual approach captures little of the efficiency benefit the initiative is meant to provide.
- Tiered autonomy captures meaningful efficiency gains for the (typically larger) share of low-risk actions, while containing risk on the smaller share of consequential ones — directly reflecting the trade-off pattern discussed in [`ai-genai/ai-agents.md`](../ai-genai/ai-agents.md).

## Consequences
- Each tool/action available to the agent is explicitly classified by risk tier as part of onboarding it onto the platform (see [`ai-genai/mcp.md`](../ai-genai/mcp.md) for the tool-scoping discipline if implemented via MCP servers).
- Full reasoning-chain observability instrumented for every agent action, regardless of tier, to support both debugging and ongoing evaluation.
- Bounded iteration/cost limits set per request to prevent runaway loops.

## Risks
- Risk-tier classification itself requires careful, periodically-reviewed judgment — a miscategorized action could either unnecessarily block a safe action or wrongly permit a risky one.
- As the agent's track record grows, there will be pressure to expand autonomous scope — any such expansion should go through the same explicit review/ADR process, not informal drift.
