# Technology Comparison Tables

**Label:** `Reference Architecture` / `Interview Preparation`

These are **conceptual comparisons for architectural reasoning**, not claims of exact feature equivalence — cloud services rarely map 1:1, and interviewers generally respect a candidate who notes that explicitly.

## Azure vs. GCP — Organizational Hierarchy

| Azure | GCP | Notes |
|---|---|---|
| Management Groups | Folders / Organization | Both provide policy inheritance; GCP's hierarchy is somewhat flatter in practice |
| Subscription | Project | GCP projects are typically more numerous/granular by design |
| RBAC | IAM | Conceptually similar; GCP IAM Conditions ≈ Azure ABAC conditions |
| Azure Policy | Organization Policy | Azure Policy has richer custom-effect expressiveness |

## Azure vs. GCP — Broader Platform

| Category | Azure | GCP | Notes |
|---|---|---|---|
| Managed Kubernetes | AKS | GKE (Standard / Autopilot) | GKE Autopilot removes node management entirely; AKS has no direct equivalent |
| Monitoring | Azure Monitor / Log Analytics | Cloud Monitoring / Cloud Logging | Comparable maturity; GCP has strong native BigQuery export for analytics |
| Firewall | Azure Firewall | Cloud Firewall Rules / Cloud NGFW | Azure Firewall Premium adds TLS inspection/IDPS as an integrated service |
| Identity Federation | Managed Identity | Workload Identity Federation | GCP's is explicitly built for federation from external IdPs; Azure Managed Identity is Azure-compute-bound |
| Data Exfiltration Control | (No direct equivalent) | VPC Service Controls | Notable GCP-distinctive capability |
| Terraform | Same tool, different provider | Same tool, different provider | Terraform itself is cloud-agnostic; module patterns differ |

## AKS vs. GKE

| Dimension | AKS | GKE |
|---|---|---|
| Node management | Customer manages node pools (or uses node auto-provisioning) | Standard: customer manages; Autopilot: fully managed by Google |
| Billing | Per-node | Standard: per-node; Autopilot: per-pod resource request |
| Workload identity | Azure AD Workload Identity | GKE Workload Identity |
| Best fit | Azure-standardized orgs, teams wanting full node control | GCP-standardized orgs; Autopilot is a strong low-ops default |

## RAG vs. Fine-Tuning

| Dimension | RAG | Fine-Tuning |
|---|---|---|
| Best for | Grounding responses in current/proprietary facts | Adapting model behavior, style, or output format |
| Data freshness | Update the index; no retraining needed | Requires retraining/re-tuning to reflect new data |
| Cost profile | Retrieval infrastructure + per-query token cost | Training cost upfront, potentially cheaper per-query after |
| Explainability | Can cite retrieved sources | Harder to trace why the model behaves a certain way |
| Typical choice | Default starting point for most enterprise knowledge use cases | Narrow, well-justified cases needing consistent behavior/format at scale |

See [`ai-genai/rag.md`](ai-genai/rag.md) for the fuller treatment.

## Agent vs. Workflow Automation

| Dimension | Agent | Workflow Automation |
|---|---|---|
| Control flow | Model dynamically decides the sequence of actions | Predetermined sequence; model fills in specific steps |
| Reliability | Lower — depends on model reasoning at each step | Higher — deterministic sequence, easier to test |
| Flexibility | Handles ambiguous, variable tasks well | Best for well-understood, repeatable processes |
| Risk profile | Higher — requires strong tool-scoping and human-in-the-loop gates | Lower — easier to bound and audit |
| Interview framing | "I'd default to workflow automation unless the task genuinely requires dynamic reasoning" | — |

See [`ai-genai/ai-agents.md`](ai-genai/ai-agents.md).

## MCP vs. Traditional API Integration

| Dimension | MCP | Traditional Point-to-Point API Integration |
|---|---|---|
| Reusability | One server implementation serves any compliant client | Bespoke integration per application |
| Standardization | Common protocol/interface | Ad hoc, varies per integration |
| Optimization | General-purpose by design | Can be tightly optimized for one specific use case |
| Security | Depends entirely on server implementation's scoping/auth | Depends entirely on the specific integration's design |
| Interview framing | "MCP reduces long-term integration maintenance as the number of tools/clients grows; for a genuinely single-use case, bespoke integration may still be simpler" | — |

See [`ai-genai/mcp.md`](ai-genai/mcp.md).

## Kubernetes vs. Managed PaaS

| Dimension | Kubernetes (AKS/GKE) | Managed PaaS (App Service / Cloud Run) |
|---|---|---|
| Control | High — custom operators, networking, scheduling | Lower — platform-managed |
| Operational burden | Higher, even when managed | Minimal |
| Best fit | Complex, multi-service, ecosystem-dependent workloads with genuine Kubernetes need | Standard stateless services/APIs |

See [`azure/compute.md`](azure/compute.md) and [`gcp/compute.md`](gcp/compute.md).

## Terraform vs. Native IaC (ARM/Bicep, Deployment Manager)

| Dimension | Terraform | Native Cloud IaC |
|---|---|---|
| Multi-cloud | Yes — one tool, many providers | No — cloud-specific |
| Day-one feature support | Sometimes slightly behind native tooling for brand-new services | Usually first to support new services |
| Ecosystem/community | Very large | Smaller, cloud-specific |
| Typical choice | Default for multi-cloud or cloud-agnostic-tooling organizations | Sometimes preferred for single-cloud orgs wanting first-day feature parity |
