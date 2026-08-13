# GCP Vertex AI & Gemini

**Label:** `Learning / Overview`

## What It Is
Google Cloud's unified AI/ML platform: Vertex AI (model training, deployment, and serving infrastructure spanning both custom ML models and generative AI), with access to Google's Gemini model family alongside a broader model garden of first- and third-party models.

## Why Architects Use It
For GCP-standardized organizations, Vertex AI provides the same enterprise-governance value proposition as Azure AI Foundry — private connectivity, IAM integration, and a consistent platform spanning both traditional ML and GenAI workloads, which is a notable differentiator versus a GenAI-only platform.

## Architecture Considerations
- **Unified ML + GenAI platform**: Vertex AI's ability to serve both traditional custom-trained ML models and generative models through consistent tooling (pipelines, model registry, monitoring) is architecturally distinctive versus platforms that only cover generative use cases — relevant for organizations with existing traditional ML investment.
- **Model Garden**: access to multiple model families (Gemini, open models, third-party) through one platform, allowing model routing/selection strategy (per [`ai-architecture.md`](ai-architecture.md)) to be implemented without integrating multiple separate vendor SDKs.
- **Grounding**: Vertex AI's built-in grounding capability (connecting model responses to Google Search or an enterprise's own data) is a productized alternative to building a fully custom RAG pipeline for some use cases — evaluate against a custom RAG build based on the specific control/customization needed (see [`rag.md`](rag.md)).
- **Private Service Connect integration**: Vertex AI endpoints can be accessed via PSC for private connectivity, consistent with the broader GCP networking model in [`gcp/networking.md`](../gcp/networking.md).

## Common Mistakes
- Building a fully custom RAG pipeline when Vertex AI's built-in grounding would meet the requirement with substantially less engineering effort.
- Not leveraging the unified model registry/monitoring across traditional ML and GenAI workloads, running them as entirely separate, duplicated platforms.
- Assuming Gemini and Azure OpenAI's underlying models behave identically in prompt engineering/output characteristics — they don't, and prompts often need tuning per provider.

## Principal-Level Questions
- When would Vertex AI's built-in grounding be sufficient versus needing a fully custom RAG architecture?
- How would you design a multi-cloud AI strategy leveraging both Azure AI Foundry and Vertex AI, and what would justify that complexity?

## Scenario Question
> An organization already has a mature traditional ML platform on Vertex AI and now wants to add generative AI capabilities without standing up an entirely separate platform.

## Strong Answer (Outline)
1. Leverage Vertex AI's unified platform: extend the existing model registry, monitoring, and pipeline tooling to cover generative model deployment/serving rather than adopting a separate GenAI-specific platform.
2. Evaluate built-in grounding against the specific use case's retrieval requirements before building custom RAG — if grounding against enterprise data with standard retrieval behavior suffices, it meaningfully reduces engineering scope versus a fully custom pipeline (see [`rag.md`](rag.md) for when custom RAG is actually justified).
3. Reuse existing IAM, networking (Shared VPC/PSC), and observability patterns already established for the traditional ML platform, avoiding a parallel, inconsistent GenAI-specific stack.
4. Extend existing model governance processes (approval, evaluation, monitoring) to cover generative models' distinct risk profile (hallucination, prompt injection) rather than assuming traditional ML governance fully covers it unmodified.

## Follow-Up Questions
- What genuinely justifies building custom RAG instead of using built-in grounding?
- How would you extend traditional ML monitoring practices to cover generative-model-specific failure modes?

## Trade-offs
- Vertex AI's unified platform: strong reuse of existing ML platform investment, ties the organization more tightly to GCP's specific tooling and model availability.
