# LLMOps — Lifecycle, Evaluation, Observability, Cost

**Label:** `Learning / Overview`

## What It Is
The operational discipline around running GenAI/LLM-powered features in production: model lifecycle management (versioning, deprecation), systematic evaluation (quality, safety, regression testing), production observability (tracing, quality monitoring), and cost management — the MLOps discipline adapted for the specific characteristics of LLM-based systems.

## Why Architects Use It
Without LLMOps discipline, GenAI features tend to be built once, evaluated informally, and then run with no systematic way to detect quality regression, cost growth, or safety drift over time — LLMOps applies the same production rigor expected of any other system to this newer class of workload.

## Architecture Considerations
- **Evaluation as a continuous, automated process, not a launch gate only**: maintain a representative evaluation dataset (including edge cases and known-difficult examples) and run it automatically on every prompt/model/pipeline change, not just at initial launch — model provider updates and pipeline changes can silently regress quality otherwise.
- **Multiple evaluation dimensions**: relevance/quality of output, faithfulness/groundedness (for RAG, does the output actually reflect the retrieved source), safety (harmful content, prompt injection resistance), and latency/cost — a system that scores well on one dimension can still be failing on another.
- **Human evaluation alongside automated metrics**: automated metrics (including LLM-as-judge approaches) are useful for scale and regression detection but don't fully substitute for periodic human review, especially for subjective quality dimensions.
- **Production observability**: full request/response tracing (including intermediate steps for RAG/agent pipelines, per [`ai-agents.md`](ai-agents.md)) for debugging, plus ongoing quality monitoring (e.g., sampling production traffic for evaluation) to catch drift that a static evaluation set might miss.
- **Cost observability**: token usage and cost tracked per feature/team, feeding into the FinOps practices in [`architecture/finops.md`](../architecture/finops.md).

## Common Mistakes
- Evaluating only at launch, with no regression detection as prompts, models, or retrieval pipelines change over time.
- Relying solely on automated/LLM-as-judge evaluation with no periodic human review, missing subjective quality issues automated metrics don't catch well.
- No production tracing for multi-step pipelines (RAG, agents), making failure diagnosis largely guesswork.
- No cost attribution per feature/team, discovering runaway spend only when the overall cloud bill spikes.

## Principal-Level Questions
- How would you monitor and evaluate LLM applications in production, beyond a one-time launch evaluation?
- How would you detect a silent quality regression caused by an underlying model provider update you didn't initiate?

## Scenario Question
> How would you monitor and evaluate LLM applications?

## Strong Answer (Outline)
1. Build and maintain a representative evaluation dataset (diverse queries, known-difficult edge cases, adversarial examples) reviewed and expanded over time as new failure modes are discovered in production.
2. Run this evaluation set automatically on every meaningful change (prompt edits, retrieval pipeline changes, and — where feasible — on a schedule to catch upstream model provider updates) as a CI-style regression gate.
3. Evaluate across multiple dimensions explicitly: relevance/quality, faithfulness (for RAG), safety, latency, and cost — a single aggregate score hides which dimension actually regressed.
4. Instrument full request tracing in production (see [`kubernetes/observability.md`](../kubernetes/observability.md) principles applied to LLM pipelines), sampling a portion of live traffic for ongoing human review to catch drift the static evaluation set doesn't cover.
5. Set up alerting on quality-proxy signals available in real time (e.g., user feedback/thumbs-down rate, fallback/error rate, cost per request trending) since full evaluation-set-quality scoring typically can't run on every live request.

## Follow-Up Questions
- How would you distinguish a genuine model provider quality regression from a change in the nature of incoming user queries?
- How do you balance evaluation dataset size/coverage against the cost and time of running it on every change?

## Trade-offs
- Comprehensive continuous evaluation: much stronger quality/safety confidence over time, real ongoing engineering investment to build and maintain the evaluation infrastructure and dataset — proportion the investment to the feature's actual business risk/exposure.
