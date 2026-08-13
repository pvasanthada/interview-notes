# Retrieval-Augmented Generation (RAG)

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
An architecture pattern where a model's response is grounded in retrieved, relevant context (typically from a vector database or search index over an organization's own data) rather than relying solely on the model's training-time knowledge — retrieval happens at request time and the retrieved content is injected into the prompt.

## Why Architects Use It
RAG addresses two core limitations of using a foundation model alone: knowledge cutoff (the model doesn't know an organization's private/current data) and hallucination reduction (grounding responses in retrieved source material makes outputs more verifiable and citable) — without the cost/complexity of fine-tuning a model on proprietary data.

## Architecture Considerations
- **Pipeline stages**: ingestion (chunking documents into retrievable units) → embedding (converting chunks to vectors) → indexing (storing in a vector database, see [`vector-databases.md`](vector-databases.md)) → retrieval (similarity search against the query) → augmentation (injecting retrieved content into the prompt) → generation.
- **Chunking strategy** materially affects retrieval quality — chunk size/overlap needs tuning per content type (dense technical docs vs. conversational content behave differently); this is often the highest-leverage tuning point in a RAG system, more so than model selection.
- **Hybrid search**: combining vector (semantic) search with traditional keyword/lexical search (e.g., BM25) frequently outperforms vector search alone, especially for queries with exact-match requirements (product codes, specific terminology) that pure semantic similarity handles poorly.
- **Re-ranking**: an additional re-ranking step after initial retrieval (using a smaller, cheaper model or algorithm) to reorder results by relevance before sending to the generation step, improving quality without needing a larger context window.
- **Freshness/update strategy**: how retrieved data stays current — real-time indexing vs. scheduled batch re-indexing, based on how quickly the source data actually changes.
- **RAG vs. fine-tuning**: RAG is generally preferred when the need is grounding in current/proprietary *facts*; fine-tuning is more appropriate for adapting model *behavior/style/format* — see [`comparison-tables.md`](../comparison-tables.md) at the repo root.

## Common Mistakes
- Naive chunking (fixed-size splits ignoring document structure) producing poor retrieval quality, then blaming the model rather than the retrieval pipeline.
- Vector-only search when hybrid search would substantially improve results for exact-match-sensitive queries.
- No evaluation framework — shipping RAG changes based on spot-checking a handful of examples rather than a systematic evaluation set (see [`llmops.md`](llmops.md)).
- Treating RAG as a one-time build rather than an ongoing system requiring index freshness maintenance and continuous quality monitoring.

## Principal-Level Questions
- Design an enterprise RAG platform serving multiple business units with different data sensitivity levels.
- How would you diagnose whether poor RAG output quality stems from retrieval or generation?

## Scenario Question
> Design an enterprise RAG platform.

## Strong Answer (Outline)
1. **Data layer**: source documents ingested with sensitivity/business-unit metadata preserved, chunked with a strategy tuned per content type (not one-size-fits-all).
2. **Access control at retrieval time**: retrieval must respect the requesting user's actual data access permissions (not just the application's) — a common, serious real-world gap where RAG systems inadvertently surface content the requesting user shouldn't see; implement via metadata filtering enforced at the vector database query layer, not just at the application UI layer.
3. **Retrieval**: hybrid search (vector + keyword) with a re-ranking step, tuned and evaluated against a representative query set per business unit's actual usage patterns.
4. **Generation**: prompt template injecting retrieved content with clear source attribution, enabling the response to cite sources — improves trust and provides a mechanism to verify grounding.
5. **Evaluation and monitoring**: an ongoing evaluation pipeline (retrieval relevance metrics + generation quality/faithfulness metrics) rather than a one-time launch validation, with alerting on quality regression.
6. **Governance**: clear labeling of this as `Reference Architecture`/`Hands-on-POC` maturity in any interview discussion unless genuinely operated in production at scale.

## Follow-Up Questions
- How would you handle a business unit's data that must never be retrievable by another business unit's users, even indirectly?
- How would you diagnose a specific case where RAG confidently produced an incorrect answer?

## Trade-offs
- RAG: keeps responses current without retraining, real engineering investment in the retrieval pipeline and ongoing index maintenance that's easy to underestimate.
