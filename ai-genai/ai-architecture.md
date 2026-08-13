# AI/GenAI Infrastructure Architecture

**Label:** `Reference Architecture`

## What It Is
The infrastructure and platform patterns underlying production GenAI systems: model access (hosted API vs. self-hosted), the request pipeline (prompt construction, retrieval, generation, post-processing), scaling/cost controls, and the operational tooling (evaluation, observability) that wraps around a raw model call.

## Why Architects Use It
GenAI capability is now widely accessible via API; the actual architectural challenge for a cloud/platform architect is the surrounding infrastructure — cost control, latency, reliability, security, and governance — not the model itself.

## Architecture Considerations
- **Hosted API (Azure OpenAI, Vertex AI) vs. self-hosted open models**: hosted APIs remove model-ops burden (scaling, GPU management) at the cost of per-token pricing and less control over data handling specifics; self-hosting is justified by strict data residency/isolation requirements, very high sustained volume where it's cost-competitive, or the need for model customization beyond what a hosted API allows.
- **Request pipeline as a first-class system**: treat the prompt-construction → retrieval (if RAG) → generation → validation/post-processing pipeline as a real distributed system requiring the same reliability engineering as any other service — timeouts, retries, circuit breakers, fallback behavior when the model provider is degraded.
- **Cost architecture**: token-based pricing means cost scales directly with usage in a way traditional compute doesn't always — architects need caching (semantic caching for repeated/similar queries), prompt/context size discipline, and model-tier selection (cheaper/faster models for simpler tasks, routing only complex requests to the most capable/expensive model).
- **Statelessness and conversation state**: LLM APIs are typically stateless per call — conversation history/context must be explicitly managed by the application layer, with implications for storage, privacy, and context-window limits.

## Common Mistakes
- Treating a GenAI feature as "just an API call" without the same reliability/observability rigor as any other production dependency.
- No cost controls (rate limits, model-tier routing, caching), leading to runaway spend as usage scales.
- No fallback/degradation behavior when the model provider has an outage or rate-limits the application.
- Over-engineering for a genuinely simple use case that a smaller/cheaper model or even non-LLM approach would solve more reliably and cheaply.

## Principal-Level Questions
- How would you design a GenAI feature's infrastructure to be resilient to a model provider outage?
- When would you choose a self-hosted open model over a hosted API, concretely?

## Scenario Question
> A GenAI-powered customer support feature has grown from a pilot to production scale, and costs have grown far faster than usage, with unpredictable latency during peak hours.

## Strong Answer (Outline)
1. Cost: introduce semantic caching for repeated/similar queries, and route requests by complexity — a lightweight classification step directs simple queries to a smaller/cheaper model, reserving the most capable (expensive) model for genuinely complex requests.
2. Latency: implement request queuing/backpressure with clear timeout and fallback behavior (e.g., a cached or template response) rather than letting requests queue indefinitely during peak load; evaluate provisioned throughput/reserved capacity if the hosted API supports it for predictable peak volume.
3. Reliability: add circuit-breaker behavior so a degraded/unavailable model provider fails gracefully (fallback to a simpler rule-based response or a clear "temporarily unavailable" state) rather than cascading into broader application failure.
4. Governance: instrument token usage and cost per feature/team for ongoing visibility (FinOps for AI, see [`architecture/finops.md`](../architecture/finops.md)), catching cost growth trends before they become a crisis.

## Follow-Up Questions
- How would you decide when self-hosting an open model becomes cost-justified at this scale?
- How do you evaluate whether a cheaper/smaller model change maintains acceptable output quality?

## Trade-offs
- Hosted API: fastest to build, ongoing per-token cost and less infrastructure control.
- Self-hosted: more control and potentially better unit economics at high sustained scale, real GPU infrastructure and model-ops burden.
