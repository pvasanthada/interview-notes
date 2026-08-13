# Diagrams

**Label:** `Reference Architecture`

Simple, professional diagrams supporting the whiteboard interview framework ([`docs/architecture-interview-framework.md`](../docs/architecture-interview-framework.md)). Mermaid diagrams render natively in GitHub; draw.io files can be opened at [app.diagrams.net](https://app.diagrams.net) or the draw.io desktop app.

## Mermaid Diagrams (inline, GitHub-renderable)

- Enterprise cloud landing zone — see below
- Hub-spoke network topology — see below
- Kubernetes platform architecture — see below
- Terraform enterprise workflow — see below
- RAG pipeline — see below
- MCP architecture — see below

## draw.io Files

- [`architecture-interview-framework.drawio`](architecture-interview-framework.drawio) — the 13-step whiteboard method as a visual flow
- [`cloud-architecture-interview.drawio`](cloud-architecture-interview.drawio) — generic 3-tier cloud architecture template
- [`system-design-framework.drawio`](system-design-framework.drawio) — the 10-point system design framework as a visual flow

---

## Enterprise Landing Zone (Mermaid)

```mermaid
flowchart TD
    Root[Tenant Root Management Group] --> Platform[Platform]
    Root --> LandingZones[Landing Zones]
    Root --> Sandbox[Sandbox]
    Root --> Decommissioned[Decommissioned]

    Platform --> Identity[Identity Subscription]
    Platform --> Management[Management Subscription]
    Platform --> Connectivity[Connectivity Subscription]

    LandingZones --> Corp[Corp Archetype]
    LandingZones --> Online[Online Archetype]

    Corp --> App1[Workload Subscription A]
    Corp --> App2[Workload Subscription B]
    Online --> App3[Workload Subscription C]
```

## Hub-Spoke Network Topology (Mermaid)

```mermaid
flowchart LR
    OnPrem[On-Premises] -- ExpressRoute --> Hub[Hub VNet: Firewall, DNS, Gateway]
    Hub -- Peering --> Spoke1[Spoke: App Tier A]
    Hub -- Peering --> Spoke2[Spoke: App Tier B]
    Hub -- Peering --> Spoke3[Spoke: Data Tier]
    Hub -- Egress --> Internet((Internet))
```

## Kubernetes Platform Architecture (Mermaid)

```mermaid
flowchart TD
    Dev[Developer] -- git push --> Repo[Git Repository]
    Repo -- triggers --> CI[CI: build, test, image]
    CI -- pushes --> Registry[Container Registry]
    Repo -- watched by --> GitOps[GitOps Controller: Argo CD]
    GitOps -- reconciles --> Cluster[Kubernetes Cluster]
    Registry -- pulled by --> Cluster
    Cluster --> Ingress[Ingress / Gateway]
    Ingress --> Users((Users))
    Cluster --> Observability[Prometheus / Grafana / OTel]
```

## Terraform Enterprise Workflow (Mermaid)

```mermaid
flowchart LR
    Dev[Engineer] -- opens PR --> VCS[Version Control]
    VCS -- triggers --> Plan[CI: terraform plan]
    Plan -- posts diff --> Review[Human Review]
    Review -- approve & merge --> Apply[CI: terraform apply]
    Apply -- least-privilege identity --> Cloud[(Cloud Resources)]
    Apply --> State[(Remote State: locked, encrypted)]
```

## RAG Pipeline (Mermaid)

```mermaid
flowchart LR
    Docs[(Source Documents)] --> Chunk[Chunking]
    Chunk --> Embed[Embedding Model]
    Embed --> VectorDB[(Vector Database)]
    Query[User Query] --> Retrieve[Hybrid Retrieval + Re-rank]
    VectorDB --> Retrieve
    Retrieve -- access-control filtered --> Context[Retrieved Context]
    Context --> Prompt[Prompt Construction]
    Prompt --> LLM[LLM Generation]
    LLM --> Response[Response with Citations]
```

## MCP Architecture (Mermaid)

```mermaid
flowchart LR
    Client[AI Client / Agent] -- MCP protocol --> Server1[MCP Server: Internal Docs]
    Client -- MCP protocol --> Server2[MCP Server: Ticketing System]
    Server1 -- scoped, authenticated --> Data1[(Internal Data)]
    Server2 -- scoped, authenticated --> Data2[(Ticketing API)]
    Client --> Log[Audit Log / Monitoring]
```
