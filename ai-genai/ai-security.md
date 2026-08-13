# AI Security & Governance

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
The security and governance practices specific to GenAI systems: prompt injection defense, data leakage prevention (sensitive data appearing in prompts/outputs, or being retained/used for training by a model provider), output validation, and AI-specific governance (model approval, usage policy, and monitoring for misuse).

## Why Architects Use It
GenAI introduces novel risk classes beyond traditional application security — a model can be manipulated through its input (prompt injection) in ways a traditional application's input validation wasn't designed to catch, and its outputs can inadvertently leak sensitive information absorbed from context.

## Architecture Considerations
- **Prompt injection defense**: treat any content the model processes that originates from an untrusted source (user input, retrieved documents, tool outputs) as potentially adversarial — apply input/output validation, least-privilege tool scoping (see [`ai-agents.md`](ai-agents.md)), and avoid granting the model implicit trust to take consequential actions based purely on content it has read.
- **Data governance for prompts/context**: understand and control what data enters a prompt (including retrieved RAG content and conversation history) — sensitive data in a prompt may be logged, may leave the network boundary depending on the deployment model, and needs the same data classification discipline as any other data flow.
- **Output validation**: don't treat model output as inherently safe to act on or display — validate/sanitize outputs before they're used to take actions (especially agent tool calls) or displayed in contexts where injected content could be misinterpreted (e.g., rendered as executable code or markup).
- **Governance layer**: an AI usage policy defining approved models/providers, data classification rules for what can/can't be sent to which model tier, and a review process for new AI feature launches — mirrors the broader security governance practice in [`security/security-governance.md`](../security/security-governance.md), applied to this specific risk class.
- **AI-specific CSPM/monitoring**: extend existing security monitoring to cover AI-specific signals — unusual prompt patterns, anomalous tool-call sequences from an agent, spikes in a specific failure/refusal category that might indicate probing.

## Common Mistakes
- Assuming a model's built-in safety filtering (see [`azure-ai.md`](azure-ai.md)) is sufficient defense against prompt injection or data leakage without additional application-layer controls.
- No data classification discipline for what enters prompts, risking sensitive data traveling through logging/telemetry paths not designed for that sensitivity level.
- Trusting model output implicitly for consequential downstream actions without validation.
- No AI-specific governance process, so new GenAI features launch without the same security/compliance review other systems require.

## Principal-Level Questions
- How would you govern AI workloads across Azure and GCP consistently?
- How would you defend an agent-based system against prompt injection originating from retrieved/untrusted content, not just direct user input?

## Scenario Question
> An AI agent with access to internal documentation was found to have executed an unintended action after processing a document containing hidden instructions.

## Strong Answer (Outline)
1. This is a textbook indirect prompt injection: the agent trusted instructions embedded in retrieved content as if they came from the legitimate user/system, and had sufficient tool access to act on them.
2. Immediate: audit and scope down the agent's tool access to the minimum required (see [`ai-agents.md`](ai-agents.md)), and add a human-approval gate for any consequential action class going forward.
3. Add input segmentation: clearly delineate and instruct the model to treat retrieved/tool-output content as data to reason about, not as instructions to follow — architecturally reinforced (not just via prompt wording) by structuring the system so consequential actions require a separate, explicit confirmation signal that untrusted content alone cannot produce.
4. Add monitoring for anomalous tool-call patterns (e.g., an action type the agent rarely takes, or one immediately following ingestion of a new/unusual document) as a detection layer, not just a prevention layer.
5. Extend this finding into the organization's AI governance review process so future agent deployments are assessed against this specific risk class before launch, not after an incident.

## Follow-Up Questions
- How would you test for prompt injection vulnerabilities before launch, analogous to penetration testing for traditional applications?
- How does this risk change for a fully autonomous agent versus a human-supervised one?

## Trade-offs
- Strict output/action validation and human-in-the-loop gates: substantially reduces the blast radius of prompt injection, adds latency/friction that must be weighed against the use case's actual risk profile.
