# Interview Notes — Principal Cloud & AI Infrastructure Architect

> A structured technical knowledge base and interview-preparation portfolio for senior and principal-level cloud architecture roles: **Principal Cloud Architect · Cloud & AI Infrastructure Architect · Cloud Platform Architect · Enterprise Cloud Architect**.

This repository contains structured interview preparation, architecture frameworks, scenario analysis, and technical reference notes for senior and principal-level cloud architecture roles. It is built around the way a principal architect actually reasons — requirements, constraints, trade-offs, security, cost, and operational reality — rather than as a list of trivia questions.

---

## 1. Purpose

This repository exists to:

- Organize deep, structured knowledge across cloud platforms, Kubernetes, Terraform, networking, security, platform engineering, AI/GenAI infrastructure, and architecture practice.
- Provide reusable **frameworks** for answering technical, scenario, architecture, and behavioral interview questions at a senior/principal level.
- Provide a **scenario library** that mirrors the kind of whiteboard and design problems asked at Staff/Principal/Distinguished Architect level.
- Provide a large, progressively-difficult **question bank** for rapid self-testing.
- Clearly separate genuine hands-on depth from conceptual/reference knowledge, especially in fast-moving areas like GenAI and MCP.

This is **not** a generic "top 100 interview questions" dump. It is a working knowledge base that happens to be organized for interview preparation.

## 2. Target Roles

- Principal Cloud Architect
- Cloud & AI Infrastructure Architect
- Cloud Platform Architect
- Enterprise Cloud Architect
- Staff/Principal Engineer (Cloud, Platform, Infrastructure)

## 3. Core Domains Covered

| Domain | Folder |
|---|---|
| Azure architecture | [`azure/`](azure/) |
| GCP architecture | [`gcp/`](gcp/) |
| Kubernetes | [`kubernetes/`](kubernetes/) |
| Terraform / IaC | [`terraform/`](terraform/) |
| Networking | [`networking/`](networking/) |
| Security | [`security/`](security/) |
| Platform Engineering | [`platform-engineering/`](platform-engineering/) |
| AI / GenAI Infrastructure | [`ai-genai/`](ai-genai/) |
| Architecture & System Design | [`architecture/`](architecture/) |
| Leadership & Behavioral | [`leadership/`](leadership/) |
| Scenario Library | [`scenarios/`](scenarios/) |
| Frameworks & Mindset | [`docs/`](docs/) |
| Diagrams | [`diagrams/`](diagrams/) |

## 4. The Principal Architect Mindset

A recurring theme throughout this repository is the difference between an **engineer's answer** and a **principal architect's answer**:

> **Engineer:** "I would deploy Kubernetes."
>
> **Principal Architect:** "First I would determine whether Kubernetes is justified based on workload characteristics, operational maturity, platform capabilities, cost, security requirements, and team skillset — then choose the simplest platform that meets the requirement."

See [`docs/principal-architect-mindset.md`](docs/principal-architect-mindset.md) for the full treatment.

## 5. How to Use This Repository

1. **Learning / deep prep (weeks out):** Work through each domain folder in order — concept → considerations → common mistakes → scenario questions.
2. **Framework practice:** Use [`docs/answering-frameworks.md`](docs/answering-frameworks.md) to structure your own answers out loud, not just read them.
3. **Scenario drills:** Use [`scenarios/`](scenarios/) for mock whiteboard sessions.
4. **Mock interviews:** Use [`docs/architecture-interview-framework.md`](docs/architecture-interview-framework.md) and the mock interview rounds referenced there.
5. **Day-of review (30–60 min before):** Use [`quick-revision.md`](quick-revision.md) only.

## 6. Knowledge Maturity Labels

Every non-trivial topic in this repository is labeled with one of the following, so the material is never misrepresented as production experience it is not:

| Label | Meaning |
|---|---|
| `Production Experience` | Built, operated, or owned this in a live production environment |
| `Hands-on / POC` | Built and tested in a proof-of-concept, lab, or non-production environment |
| `Learning / Overview` | Conceptual understanding, self-study, or exposure without hands-on build |
| `Reference Architecture` | Documented design pattern used for reasoning/interview purposes, not necessarily built |
| `Interview Preparation` | Structured explanation prepared specifically to communicate the above clearly in an interview |

AI, MCP, LLMOps, and other emerging technologies are deliberately **not** overstated — see the [Portfolio Disclaimer](#8-portfolio-disclaimer) below.

## 7. Technology Coverage Snapshot

- **Cloud platforms:** Azure (primary), GCP (secondary), multi-cloud patterns
- **Platform:** Kubernetes (AKS/GKE), Terraform, GitOps, Internal Developer Platforms
- **Security:** Zero Trust, IAM/RBAC/PIM, workload identity, CSPM, secrets management
- **Architecture:** HA/DR, observability, FinOps, system design, ADRs
- **AI/GenAI:** RAG, vector databases, AI agents, MCP, LLMOps — labeled per maturity above
- **Leadership:** Stakeholder management, technical decision-making, STAR-based behavioral prep

## 8. Portfolio Disclaimer

> This repository is a personal technical knowledge and interview-preparation portfolio. Examples involving emerging technologies such as GenAI, MCP, LLMOps, and AI agents may represent learning exercises, proofs of concept, or reference architectures rather than production implementations. Knowledge maturity is labeled explicitly throughout using the scale in Section 6.

No employer names, client names, confidential data, credentials, secrets, or personal identifiers appear anywhere in this repository. See [CONTRIBUTING.md](CONTRIBUTING.md) for the standards this content is held to.

## 9. Repository Map

```text
interview-notes/
├── docs/                    Frameworks, mindset, methodology
├── azure/                   Azure architecture deep dives
├── gcp/                     GCP architecture deep dives
├── kubernetes/              Kubernetes architecture & operations
├── terraform/                Terraform / IaC architecture
├── networking/               Cloud & hybrid networking
├── security/                 Cloud security & Zero Trust
├── platform-engineering/     Internal developer platforms
├── ai-genai/                 AI/GenAI infrastructure & architecture
├── architecture/             System design, HA/DR, observability, FinOps
├── leadership/                Behavioral & leadership prep
├── scenarios/                 Cross-domain scenario library
├── diagrams/                  Mermaid / draw.io diagrams
├── quick-revision.md
├── interview-roadmap.md
├── question-bank.md
├── comparison-tables.md
├── adr/
└── mock-interviews.md
```

---

*Maintained as a living document. Last structured update: 2026.*
