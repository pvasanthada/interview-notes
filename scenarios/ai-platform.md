# Scenario: AI Platform Build-Out

**Label:** `Interview Preparation` (underlying technical content is `Reference Architecture` / `Learning-Overview` — see [`ai-genai/README.md`](../ai-genai/README.md) disclaimer)

## Prompt
> Design a secure AI platform allowing multiple business units to build GenAI features (RAG-based assistants, agent-based automation) on shared infrastructure, with appropriate data isolation, cost control, and governance.

## Context
Multiple business units, varying data sensitivity, shared underlying platform — combines patterns from [`ai-genai/`](../ai-genai/), [`security/`](../security/), and [`platform-engineering/`](../platform-engineering/).

## Problem
Provide a shared, efficient AI platform without one business unit's data being retrievable by another's users, without unbounded cost growth, and without each business unit rebuilding the same RAG/agent infrastructure independently.

## Analysis
This is structurally similar to any shared multi-tenant platform problem (see [`kubernetes/security-rbac.md`](../kubernetes/security-rbac.md) for the Kubernetes multi-tenancy analog) with AI-specific additions: retrieval-time access control (per [`ai-genai/rag.md`](../ai-genai/rag.md)) and token-cost governance (per [`ai-genai/ai-architecture.md`](../ai-genai/ai-architecture.md)).

## Decision
1. **Platform layer (shared, centrally owned)**: a common RAG/agent pipeline framework (ingestion, embedding, retrieval, generation, evaluation tooling per [`ai-genai/llmops.md`](../ai-genai/llmops.md)) that business units configure rather than rebuild — this is the platform-engineering golden-path pattern (see [`platform-engineering/golden-paths.md`](../platform-engineering/golden-paths.md)) applied to AI infrastructure.
2. **Data isolation**: each business unit's data indexed with strict access-control metadata; retrieval queries filtered by the requesting user's actual permissions at the vector database query layer, never relying on application-layer filtering alone (per [`ai-genai/rag.md`](../ai-genai/rag.md) and [`ai-genai/vector-databases.md`](../ai-genai/vector-databases.md)) — this is the single most important control to get right, since a shared platform materially raises the stakes of a retrieval-access-control gap.
3. **Cost governance**: token usage tracked and attributed per business unit/feature (per [`architecture/finops.md`](../architecture/finops.md)), with model-tier routing and semantic caching applied platform-wide so every business unit benefits from the same cost controls without individually implementing them.
4. **Agent tool-scoping**: any agent capability exposed on the platform (per [`ai-genai/ai-agents.md`](../ai-genai/ai-agents.md)) is scoped per business unit's own systems/data — no shared, overly-broad tool access across business units.
5. **Governance**: a lightweight AI feature review process (per [`ai-genai/ai-security.md`](../ai-genai/ai-security.md)) required before a new business-unit feature launches on the platform, covering data classification, tool access scope, and evaluation coverage — proportional to the feature's actual risk (consequential agent actions get more scrutiny than a read-only RAG assistant).

## Trade-offs
- A shared platform reduces duplicated engineering effort significantly, but the access-control and cost-governance requirements are non-negotiable given the shared infrastructure — the platform's value depends entirely on getting these right, more so than in a single-tenant AI deployment.

## Honest Maturity Framing for Interview Delivery
This is a `Reference Architecture` answer demonstrating sound design reasoning, not a claim of having operated a production platform at this scale — state that explicitly and let the quality of the reasoning carry the answer (see [`ai-genai/README.md`](../ai-genai/README.md)).

## Follow-Up Depth for Interviews
Be ready to go deeper on: how you'd test/validate the retrieval-time access control before launch, how model-tier routing decisions get made (per [`ai-genai/ai-architecture.md`](../ai-genai/ai-architecture.md)), and how the evaluation framework differs for a RAG assistant versus an agent-based feature (per [`ai-genai/llmops.md`](../ai-genai/llmops.md) and [`ai-genai/ai-agents.md`](../ai-genai/ai-agents.md)).
