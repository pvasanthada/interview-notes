# Azure AI — Azure OpenAI & Azure AI Foundry

**Label:** `Learning / Overview`

## What It Is
Azure's GenAI platform surface: Azure OpenAI Service (hosted access to OpenAI models with Azure's enterprise controls — VNet integration, private endpoints, Entra ID auth, regional data residency options), and Azure AI Foundry (Microsoft's broader platform for building, evaluating, and operationalizing AI applications, including model catalog access beyond OpenAI models, prompt flow tooling, and evaluation capabilities).

## Why Architects Use It
For organizations already standardized on Azure, Azure OpenAI/AI Foundry provides the enterprise controls (network isolation, identity integration, compliance certifications) that a raw public API doesn't, without needing to build that governance layer from scratch.

## Architecture Considerations
- **Enterprise controls as the actual value-add**: the differentiator over calling OpenAI's API directly is Azure-native network isolation (private endpoints), managed-identity-based auth (no API key sprawl), and integration with existing Azure governance (Policy, Defender for Cloud) — architects should lead with this rather than the model capability itself, which is broadly similar across providers.
- **Regional availability and model version management**: model availability varies by region and evolves quickly — design for graceful handling of model deprecation/version transitions, not a hardcoded model version dependency.
- **Content filtering**: Azure OpenAI includes built-in content filtering as a default safety layer — understand what it catches and its configurability, and don't assume it replaces application-level output validation for domain-specific concerns.
- **Provisioned throughput vs. pay-as-you-go**: for predictable, high-volume production workloads, provisioned throughput gives cost/latency predictability at the cost of committing to reserved capacity — a FinOps trade-off similar in spirit to reserved instances.

## Common Mistakes
- Treating Azure OpenAI as functionally identical to calling OpenAI's public API directly, missing the actual reason to use it (enterprise network/identity controls).
- Hardcoding a specific model version without a plan for deprecation/migration.
- Assuming built-in content filtering is sufficient for all safety/compliance needs without domain-specific validation.

## Principal-Level Questions
- What's the actual architectural justification for using Azure OpenAI over calling a model provider's API directly?
- How would you design for model deprecation/version migration without disrupting production?

## Scenario Question
> A team wants to deploy a GenAI feature that must never send data outside the company's private network, and must use existing Entra ID-based access control.

## Strong Answer (Outline)
1. Azure OpenAI Service deployed with a private endpoint (see [`networking/private-connectivity.md`](../networking/private-connectivity.md)), ensuring the model call path never traverses the public internet.
2. Authentication via managed identity/Entra ID rather than API keys, integrating directly with the org's existing identity/RBAC model.
3. Data handling: confirm and document the service's data-use policy (Azure OpenAI's enterprise terms specify prompts/completions aren't used to train the base models) as part of the compliance record, since this is frequently a specific stakeholder concern that needs an explicit, accurate answer rather than an assumption.
4. Content filtering configured per the sensitivity of the use case, layered with application-level validation for domain-specific requirements the built-in filter doesn't cover.

## Follow-Up Questions
- How would you handle a requirement for a model not currently available in an approved data-residency region?
- How would you evaluate whether provisioned throughput is cost-justified for this workload's volume?

## Trade-offs
- Azure OpenAI/AI Foundry: strong enterprise governance fit, tied to Azure's model availability/region footprint and pricing versus a multi-provider approach.
