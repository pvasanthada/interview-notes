# AI/GenAI — Scenario Questions

**Label:** `Interview Preparation`

Remember to lead with an honest maturity framing per the [domain README](README.md) before diving into the architecture.

## Foundation
1. Explain the difference between RAG and fine-tuning, and when you'd choose each.
2. What is prompt injection, and why is it fundamentally different from traditional application input validation problems?

## Intermediate
3. A RAG system is returning confidently incorrect answers. Diagnose whether the issue is retrieval or generation.
4. Design a cost-control strategy for a GenAI feature whose usage is growing rapidly.

## Senior
5. Design an enterprise RAG platform serving multiple business units with different data sensitivity levels.
6. How would you secure MCP-based AI agents against a compromised or overly-broad server?
7. Design a strategy to monitor and evaluate LLM applications in production on an ongoing basis, not just at launch.

## Principal
8. Design a secure AI platform for multiple business units, covering data governance, access control, and cost management.
9. How would you govern AI workloads consistently across Azure and GCP?
10. Design an AI agent platform for a consequential business process (e.g., financial approvals), including where human-in-the-loop gates are required.

## Architecture Scenario
11. Design an enterprise RAG platform end to end: ingestion, retrieval, generation, access control, evaluation, and cost management.
12. Design a GenAI feature's infrastructure to be resilient to a model provider outage, including fallback behavior and cost/latency controls under peak load.
