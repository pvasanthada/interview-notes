# Vector Databases

**Label:** `Learning / Overview`

## What It Is
Purpose-built (or purpose-extended) databases for storing high-dimensional embedding vectors and performing efficient similarity search (nearest-neighbor search) against them — the retrieval backbone of a RAG system. Options span dedicated vector databases (e.g., specialized managed services), vector extensions to existing databases (e.g., a relational database with a vector index extension), and cloud-native search services with vector capability.

## Why Architects Use It
Similarity search at scale (millions+ of vectors) requires specialized indexing algorithms (e.g., approximate nearest neighbor methods) that general-purpose databases historically didn't support efficiently — though this line has blurred as mainstream databases add native vector support.

## Architecture Considerations
- **Dedicated vector DB vs. vector extension on an existing database**: a dedicated vector database often gives the best raw similarity-search performance/scale; a vector extension on a database the organization already operates (e.g., adding vector search to an existing relational or document database) reduces new operational surface and lets retrieval join naturally with existing relational metadata (useful for the access-control filtering discussed in [`rag.md`](rag.md)) — the right choice depends on scale and how much new operational complexity is acceptable versus reusing existing platform investment.
- **Approximate vs. exact nearest neighbor**: approximate methods (e.g., HNSW-based indexes) trade a small amount of recall accuracy for dramatically better performance at scale — appropriate for nearly all production RAG use cases; exact search is rarely necessary and doesn't scale.
- **Metadata filtering**: production vector search almost always needs to combine similarity search with metadata filters (access control, business unit, document type, recency) — verify the chosen solution supports efficient filtered search, not just pure vector similarity, since this is essential for the access-control pattern in [`rag.md`](rag.md).
- **Index update/freshness**: understand the chosen system's behavior for incremental updates versus requiring periodic full re-indexing, matched to how frequently source data actually changes.

## Common Mistakes
- Choosing a dedicated vector database by default without evaluating whether an extension to an already-operated database would meet the actual scale/performance need with less new operational surface.
- Ignoring metadata filtering requirements until after initial build, requiring a rearchitecture to support access control.
- Not load-testing retrieval latency at realistic production scale/concurrency before launch.

## Principal-Level Questions
- How would you decide between a dedicated vector database and adding vector search capability to an existing database platform?
- How do you ensure vector search results respect data access control at query time, not just at the application layer?

## Scenario Question
> A team wants to add a vector database for a new RAG feature, and your organization already operates a mature relational database platform.

## Strong Answer (Outline)
1. Evaluate actual scale requirements first — for many enterprise RAG use cases (thousands to low millions of chunks, not billions), a vector extension on the existing, already-operated database platform is often sufficient and avoids introducing an entirely new system to secure, back up, and operate.
2. If projected scale or query latency requirements genuinely exceed what the extension approach comfortably handles, evaluate a dedicated vector database, weighing the added operational surface against the performance/scale benefit.
3. Regardless of choice, design the schema/index to support the metadata filtering needed for access-control-aware retrieval from day one, not as a later addition.
4. Load-test retrieval latency/throughput against realistic concurrent usage before committing to the launch scale.

## Follow-Up Questions
- How would you migrate from a vector extension to a dedicated vector database later if scale outgrows it?
- How do you handle embedding model version changes, given previously-indexed vectors become incompatible with a new embedding model?

## Trade-offs
- Dedicated vector database: best raw retrieval performance at very large scale, new operational surface to secure/operate/back up.
- Vector extension on existing platform: less new operational surface, may hit performance/scale limits sooner than a purpose-built system.
