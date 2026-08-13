# ADR-006 — RAG Architecture

**Status**: Accepted (Reference Architecture — see maturity note below)

**Maturity note**: This ADR documents a `Reference Architecture` design decision, prepared for interview-preparation and architectural-reasoning purposes. See [`ai-genai/README.md`](../ai-genai/README.md) for the repository-wide maturity labeling policy.

## Context
A product team wants to build an internal knowledge assistant grounded in the organization's internal documentation, which changes moderately frequently and includes both public and sensitivity-restricted content.

## Problem
Choose a retrieval architecture that balances answer quality, data freshness, and — critically — must respect each user's existing document access permissions at query time.

## Options
1. **Pure vector similarity search, no access control filtering at retrieval time** — simplest to build, unacceptable given the sensitivity-restricted content in scope.
2. **Vector search with access-control metadata filtering enforced at the vector database query layer** (recommended) — modest added complexity, correctly enforces access control.
3. **Fine-tune a model on the internal documentation instead of RAG** — doesn't solve the access-control problem at all (a fine-tuned model can't selectively "forget" restricted content per user) and doesn't handle data freshness well; rejected for this use case (see [`ai-genai/rag.md`](../ai-genai/rag.md) and [`comparison-tables.md`](../comparison-tables.md) for the general RAG-vs-fine-tuning trade-off).

## Decision
Adopt option 2: hybrid vector + keyword retrieval with access-control metadata filtering enforced at the vector database query layer, not only at the application layer.

## Rationale
- Access-control enforcement at the retrieval layer (not just application-layer filtering after the fact) is essential given sensitivity-restricted content — application-layer-only filtering is a common and serious real-world gap (see [`ai-genai/rag.md`](../ai-genai/rag.md)).
- Hybrid (vector + keyword) retrieval outperforms vector-only search for the kind of exact-terminology queries expected against internal technical documentation.
- RAG (versus fine-tuning) accommodates the documentation's moderate change frequency without requiring retraining.

## Consequences
- Document ingestion pipeline must preserve and propagate access-control metadata from the source system through chunking and indexing.
- Retrieval queries are constructed to include the requesting user's actual permission scope as a filter, not merely a post-retrieval application-layer check.
- An ongoing evaluation framework (see [`ai-genai/llmops.md`](../ai-genai/llmops.md)) is required before launch, covering both answer quality and — critically — access-control correctness under test scenarios simulating restricted-content queries.

## Risks
- A bug in metadata propagation during ingestion could silently allow restricted content to become retrievable — requires dedicated automated testing for this specific failure mode, not just general quality evaluation.
- Retrieval-time filtering adds query complexity/latency versus unfiltered search — acceptable trade-off given the sensitivity requirement.
