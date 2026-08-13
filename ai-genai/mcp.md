# Model Context Protocol (MCP)

**Label:** `Learning / Overview`

## What It Is
An open protocol standardizing how AI applications (specifically LLM-based clients) connect to external tools, data sources, and systems — providing a common interface so a model/agent client can discover and call tools/resources exposed by an MCP server, rather than every AI application needing bespoke, one-off integration code per tool.

## Why Architects Use It
MCP addresses the "N clients × M tools" integration problem: without a standard protocol, every AI application needs custom integration code for every external system it connects to; MCP lets a tool/system expose one MCP server implementation that any MCP-compatible client can consume — directly analogous in intent to how a standard API/protocol (e.g., a database driver interface) decouples the consumer count from the integration count.

## Architecture Considerations
- **MCP vs. traditional API integration**: a traditional integration is typically point-to-point and tightly coupled to a specific application's needs; MCP standardizes the interface so the same server implementation serves any compliant client — the trade-off is that traditional bespoke integration can be more tightly optimized for a single specific use case, while MCP favors reusability and standardization across many use cases (see [`comparison-tables.md`](../comparison-tables.md)).
- **Security posture as a first-class design concern**: an MCP server, if compromised or overly broadly scoped, is effectively a capability grant to whatever the model can be prompted to do with it — treat MCP server access with the same least-privilege, authenticated, and audited discipline as any other service-to-service integration (see [`ai-security.md`](ai-security.md)).
- **Server scope discipline**: same principle as agent tool-scoping in [`ai-agents.md`](ai-agents.md) — an MCP server should expose the minimum set of capabilities/data the use case genuinely requires, not a broad, general-purpose surface "for future flexibility."
- **Transport and deployment**: MCP servers can run locally or remotely; architecturally, a remote/hosted MCP server needs the same network isolation, authentication, and monitoring considerations as any other internal service.

## Common Mistakes
- Treating MCP as inherently secure by virtue of being a "standard protocol" — the protocol standardizes the interface, not the security posture of a specific server's implementation or scope.
- Building an overly broad MCP server exposing far more capability than the actual use case needs, in the name of reusability.
- Overstating hands-on MCP production experience given how new and fast-moving this specific technology is — see the domain-wide disclaimer in [`README.md`](README.md).

## Principal-Level Questions
- How does MCP compare architecturally to traditional point-to-point API integration, and what's the actual trade-off?
- How would you secure MCP-based AI agents against a compromised or overly broad server?

## Scenario Question
> How would you secure MCP-based AI agents?

## Strong Answer (Outline)
1. Apply least-privilege scoping to every MCP server exposed to an agent — each server should expose only the specific tools/data the agent's actual task requires, mirroring the tool-scoping discipline in [`ai-agents.md`](ai-agents.md).
2. Authenticate and authorize MCP server access using the organization's existing identity infrastructure (see [`security/iam.md`](../security/iam.md)) rather than a separate, bespoke auth mechanism per server.
3. Treat MCP servers exposing write/action capabilities (not just read/data-retrieval) with the same human-in-the-loop discipline as any other consequential agent action.
4. Log and monitor MCP tool invocations with the same rigor as any privileged API call, enabling audit and anomaly detection if an agent's usage pattern deviates unexpectedly.
5. Network-isolate remote MCP servers (private connectivity, not public exposure) consistent with the organization's broader Zero Trust posture (see [`security/zero-trust.md`](../security/zero-trust.md)).

## Follow-Up Questions
- How would you evaluate whether adopting MCP is worth the standardization benefit versus a simpler, tightly-scoped bespoke integration for a specific one-off use case?
- What's your process for reviewing/approving a new MCP server before it's exposed to a production agent?

## Trade-offs
- MCP standardization: reduces long-term integration maintenance burden as the number of tools/clients grows, adds protocol-level complexity that may be unnecessary overhead for a genuinely single-use, one-off integration.
