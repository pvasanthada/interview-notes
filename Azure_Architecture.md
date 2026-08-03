# Microsoft Azure Architecture Interview Prep
### Basic → Intermediate → Advanced → Scenario/Real-Time

---

## SECTION 1: FOUNDATIONAL CONCEPTS

**Q1. What is Azure and how is it different from on-premises infrastructure?**
Azure is Microsoft's public cloud platform offering IaaS, PaaS, and SaaS. Unlike on-prem, you don't own hardware — you rent compute/storage/network on demand, pay-as-you-go, and scale elastically. Key differences: CapEx → OpEx, elastic scaling, global reach via regions, built-in HA/DR options, and a shared responsibility security model.

**Q2. Explain IaaS vs PaaS vs SaaS with Azure examples.**
- **IaaS** — you manage OS, runtime, apps; Azure manages virtualization/hardware. Example: Azure Virtual Machines, Azure Virtual Network.
- **PaaS** — Azure manages OS and runtime; you manage app + data. Example: Azure App Service, Azure SQL Database, Azure Functions.
- **SaaS** — fully managed software. Example: Microsoft 365, Dynamics 365.
Interviewers often want you to place a *given* workload correctly — e.g. "lift-and-shift a legacy .NET app with COM dependencies" → IaaS (VM); "new REST API, no OS management wanted" → PaaS (App Service).

**Q3. What are Azure Regions, Region Pairs, and Availability Zones?**
- **Region** — a geographic area with one or more datacenters (e.g., East US, Central India).
- **Region Pair** — two regions in the same geography (usually 300+ miles apart) used for platform-driven disaster recovery of some services (e.g., paired region prioritized for recovery, sequential updates so both aren't patched simultaneously).
- **Availability Zone (AZ)** — physically separate datacenters within a region, each with independent power/cooling/networking. Used for **high availability within a region** (protects against datacenter-level failure).
- **Availability Set** — logical grouping of VMs within a *single* datacenter across fault domains (power) and update domains (maintenance reboots) — protects against rack/host-level failure, not datacenter failure.

> **Interview tip:** A very common trap question is "Availability Set vs Availability Zone vs Region Pair — when do you use which?" Answer in terms of *blast radius*: rack failure → Availability Set; datacenter failure → Availability Zone; regional disaster → multi-region with Region Pair/Traffic Manager/Front Door.

**Q4. What is a Resource Group and Subscription?**
- **Resource Group (RG):** logical container for resources sharing the same lifecycle (deploy/manage/delete together). RBAC and policies can be applied at this scope.
- **Subscription:** billing + access boundary; contains multiple RGs; has quotas/limits.
- **Management Group:** groups multiple subscriptions for org-wide governance (policy, RBAC) — sits above subscriptions in the hierarchy: **Management Group → Subscription → Resource Group → Resource**.

**Q5. What is an ARM template / Bicep, and why use Infrastructure as Code (IaC)?**
ARM (Azure Resource Manager) templates are JSON declarative files describing desired resource state; Bicep is a cleaner DSL that compiles to ARM JSON. IaC gives you repeatability, version control, peer review, and drift detection — critical for enterprise architecture answers. Alternatives: Terraform (multi-cloud), Pulumi.

---

## SECTION 2: CORE COMPUTE, STORAGE & NETWORKING

**Q6. Compare Azure compute options: VM, App Service, Azure Functions, AKS, Container Instances.**

| Option | Use case | Management overhead | Scaling |
|---|---|---|---|
| Virtual Machines | Full OS control, legacy apps | High | Manual/VMSS |
| App Service | Web apps/APIs, no OS mgmt | Low | Built-in autoscale |
| Azure Functions | Event-driven, short-lived tasks | Very low | Automatic (consumption plan) |
| AKS (Kubernetes) | Microservices, complex orchestration | Medium-High | HPA/Cluster Autoscaler |
| Container Instances (ACI) | Simple, burst container workloads, no orchestration needed | Low | Manual |

**Q7. What is a Virtual Network (VNet) and how does subnetting work?**
A VNet is an isolated private network in Azure. You divide it into subnets (CIDR blocks) to segment resources (e.g., web subnet, app subnet, data subnet) and apply Network Security Groups (NSGs) per subnet/NIC for traffic control. VNets don't span regions but can be connected via VNet Peering (same or different regions) or gateways.

**Q8. NSG vs Azure Firewall vs Application Gateway (WAF) — what's the difference?**
- **NSG:** stateless-ish, L3/L4 packet filtering (IP, port, protocol) at subnet/NIC level — cheap, basic.
- **Azure Firewall:** managed, stateful L3-L7 firewall for the whole VNet/hub, supports FQDN filtering, threat intelligence, centralized policy across a hub-spoke topology.
- **Application Gateway (with WAF):** L7 load balancer for HTTP(S) — does URL-based routing, SSL termination, and (with WAF SKU) protects against OWASP Top 10 attacks (SQLi, XSS).
> Rule of thumb for interviews: NSG = network-level ACL; Azure Firewall = perimeter/egress control; App Gateway = application-layer reverse proxy + WAF for web apps.

**Q9. Load Balancer vs Application Gateway vs Traffic Manager vs Front Door — when to use each?**
- **Azure Load Balancer:** L4 (TCP/UDP), regional, distributes traffic across VMs/VMSS within a region. Good for non-HTTP or internal traffic.
- **Application Gateway:** L7, regional, HTTP(S)-aware routing + WAF.
- **Traffic Manager:** DNS-based global routing — routes clients to the closest/healthiest **region**, doesn't proxy traffic itself (just resolves DNS).
- **Azure Front Door:** L7 global entry point — anycast, SSL offload, WAF, caching (CDN-like), path-based routing across regions — effectively "Application Gateway + Traffic Manager + CDN" combined for global HTTP apps.
> **Common scenario answer:** "For a globally distributed web app needing failover + WAF + caching, use Front Door in front of regional App Gateways or App Services."

**Q10. What are the Azure Storage types and when do you use each?**
- **Blob Storage** — unstructured data (images, backups, logs); tiers: Hot/Cool/Archive for cost optimization.
- **Azure Files** — managed SMB/NFS file shares (lift-and-shift file servers).
- **Queue Storage** — simple message queuing for decoupling components.
- **Table Storage / Cosmos DB Table API** — NoSQL key-value store.
- **Disk Storage (Managed Disks)** — attached to VMs; Standard HDD/SSD, Premium SSD, Ultra Disk based on IOPS/latency needs.

**Q11. Explain redundancy options: LRS, ZRS, GRS, GZRS, RA-GRS.**
- **LRS** (Locally Redundant): 3 copies, single datacenter.
- **ZRS** (Zone Redundant): 3 copies across Availability Zones in one region.
- **GRS** (Geo-Redundant): LRS + async copy to a paired region (not readable unless failover).
- **RA-GRS**: GRS + read access to the secondary region.
- **GZRS**: ZRS + geo-replication to paired region — highest durability.
> Interview angle: this maps directly to RPO/RTO discussions — GRS/GZRS gives regional DR but async replication means some data loss (non-zero RPO) is possible during failover.

---

## SECTION 3: IDENTITY, SECURITY & GOVERNANCE

**Q12. Explain Microsoft Entra ID (Azure AD) vs RBAC vs Azure Policy.**
- **Entra ID (Azure AD):** identity provider — authentication (who are you), supports SSO, MFA, Conditional Access, B2B/B2C.
- **RBAC (Role-Based Access Control):** authorization — what can this identity *do* on which resource/scope (Owner, Contributor, Reader, or custom roles), assigned at Management Group/Subscription/RG/Resource scope.
- **Azure Policy:** governance — enforces *rules* on resource properties (e.g., "only allow VM SKUs from an approved list," "require tagging," "deny public IP creation") — it's not about who can do it, but what configurations are allowed.

**Q13. What is Managed Identity and why is it preferred over storing credentials?**
A Managed Identity is an auto-managed Entra ID identity for an Azure resource (VM, App Service, Function) allowing it to authenticate to other Azure services (e.g., Key Vault, Storage) **without any secrets/connection strings in code**. System-assigned (tied to resource lifecycle) or user-assigned (reusable, independent lifecycle). This eliminates credential leakage risk and rotation overhead — a strong answer for any "how do you securely access a database from an App Service" question.

**Q14. How would you design network security for a multi-tier application in Azure?**
Layered ("defense in depth") approach:
1. Perimeter: Azure Front Door/App Gateway with WAF for public entry.
2. Hub-spoke VNet topology: hub has Azure Firewall for centralized egress/ingress control; spokes are peered and isolated per workload.
3. NSGs on every subnet, least-privilege rules (web subnet → app subnet only on required port, app subnet → data subnet only on DB port).
4. Private Endpoints for PaaS services (SQL, Storage, Key Vault) so traffic never traverses the public internet.
5. Azure Bastion for secure RDP/SSH without exposing public IPs on VMs.
6. Key Vault for secrets/certs, accessed via Managed Identity.
7. Microsoft Defender for Cloud for continuous posture management and threat detection.

**Q15. What is the Shared Responsibility Model?**
Security responsibility splits between Microsoft and the customer, shifting based on service type:
- **IaaS:** Microsoft secures physical infra/host; customer secures OS, patches, network config, data, identity.
- **PaaS:** Microsoft also manages runtime/OS patching; customer focuses on app config, data, identity, access.
- **SaaS:** Microsoft manages almost everything; customer mainly manages data, users, and access.
Physical security and the underlying hypervisor/network fabric are **always** Microsoft's responsibility, regardless of service model.

---

## SECTION 4: HIGH AVAILABILITY, SCALABILITY & DISASTER RECOVERY

**Q16. Explain RTO and RPO and how Azure DR design changes based on them.**
- **RTO (Recovery Time Objective):** how long you can be down before it's unacceptable.
- **RPO (Recovery Point Objective):** how much data loss (measured in time) is acceptable.
Low RTO/RPO (near-zero) → active-active multi-region architecture with synchronous or near-real-time replication (expensive, complex). Higher tolerance → active-passive with Azure Site Recovery or geo-redundant storage/backup restore (cheaper, slower).

**Q17. What DR strategies exist in Azure, from cheapest to most resilient?**
1. **Backup & restore** — cheapest, highest RTO/RPO. Azure Backup to another region.
2. **Pilot light** — minimal core infra always running in DR region (e.g., DB replica), rest spun up on failover.
3. **Warm standby (active-passive)** — scaled-down but running full stack in secondary region, scaled up on failover.
4. **Active-active (Hot/Hot)** — full production capacity in 2+ regions simultaneously, traffic routed via Front Door/Traffic Manager — lowest RTO/RPO, highest cost.
**Azure Site Recovery (ASR)** automates VM-level replication/failover/failback for the first three patterns.

**Q18. How do you design a highly available web application in Azure?**
- Deploy App Service/VMSS across **Availability Zones**.
- Use **zone-redundant** Azure SQL / Cosmos DB (multi-region writes if needed).
- Put **Application Gateway v2 (zone-redundant)** or **Front Door** in front for load balancing/failover.
- Use **Redis Cache** (zone-redundant) to reduce DB load and improve resilience.
- Enable **autoscale** rules based on CPU/queue length/custom metrics.
- Use **Availability Zones within a region** for the "99.99% SLA" tier; add a **second region** if you need protection against regional outages.
- Implement **health probes** so the load balancer routes only to healthy instances.

**Q19. VM Scale Sets (VMSS) vs manual scaling — how does autoscaling work?**
VMSS manages identical VM instances as a group, auto-scaling out/in based on metrics (CPU, memory, custom Application Insights metrics) or a schedule. It integrates with Load Balancer/App Gateway automatically. This is the IaaS-equivalent of App Service's built-in autoscale, and it's a common "how do you handle traffic spikes" answer.

**Q20. How do you design for zero-downtime deployments?**
- **Deployment slots** (App Service) — deploy to a staging slot, warm it up, then swap (swap is near-instant and reversible).
- **Blue-Green deployment** — two full environments, switch traffic via Front Door/Traffic Manager/DNS.
- **Canary/rolling deployment** — gradually shift a % of traffic (Front Door weighted routing or AKS rolling updates) while monitoring error rates before full rollout.

---

## SECTION 5: DATA & INTEGRATION ARCHITECTURE

**Q21. Azure SQL Database vs Cosmos DB vs Managed Instance — how do you choose?**
- **Azure SQL Database:** PaaS relational DB, single-database or elastic pool, good for typical OLTP apps needing SQL Server compatibility without full instance features.
- **Azure SQL Managed Instance:** near-100% SQL Server compatibility (cross-DB queries, SQL Agent, linked servers) — best for lift-and-shift of complex on-prem SQL Server workloads.
- **Cosmos DB:** globally distributed, multi-model (SQL/Mongo/Cassandra/Gremlin/Table APIs), single-digit ms latency at any scale, tunable consistency levels (Strong, Bounded Staleness, Session, Consistent Prefix, Eventual) — chosen for massive scale, global distribution, and flexible schema, not for complex joins/transactions.

**Q22. Explain the 5 Cosmos DB consistency levels briefly.**
Strong (linearizable, highest consistency, highest latency) → Bounded Staleness (lag limited by time/version) → Session (consistent within a client session — most commonly used default) → Consistent Prefix (no out-of-order writes, but may be stale) → Eventual (lowest latency, no ordering guarantee). Trade-off is always consistency vs latency/availability (CAP theorem in practice).

**Q23. How do you decouple microservices in Azure? Compare Service Bus, Event Grid, Event Hub, and Queue Storage.**
- **Storage Queue:** simple FIFO-ish queue, cheap, basic (order/point-to-point).
- **Service Bus:** enterprise messaging — supports FIFO ordering, dead-lettering, sessions, topics/subscriptions (pub-sub), transactions — ideal for reliable order-sensitive workflows (e.g., order processing).
- **Event Grid:** reactive, event-driven, near-real-time routing of discrete events (e.g., "blob created") to subscribers — push-based, low latency, not for streaming volumes of telemetry.
- **Event Hub:** big-data streaming ingestion (millions of events/sec) — think IoT telemetry, clickstreams, log aggregation, typically paired with Stream Analytics/Databricks.
> Interview shorthand: **Queue = task**, **Service Bus = enterprise message with guarantees**, **Event Grid = "something happened" notification**, **Event Hub = firehose of streaming data**.

**Q24. What is API Management (APIM) and why use it?**
APIM is a gateway that sits in front of your backend APIs to provide: authentication/rate-limiting/throttling, request/response transformation, versioning, developer portal, caching, and centralized policy enforcement — decoupling API consumers from backend implementation changes. Common in microservices/B2B API exposure scenarios.

---

## SECTION 6: WELL-ARCHITECTED FRAMEWORK & COST

**Q25. What are the 5 pillars of the Azure Well-Architected Framework?**
1. **Reliability** — resiliency, availability, DR.
2. **Security** — Zero Trust, defense in depth, identity-first.
3. **Cost Optimization** — right-sizing, reserved instances, autoscaling.
4. **Operational Excellence** — DevOps, IaC, monitoring, automation.
5. **Performance Efficiency** — scalability, caching, choosing the right SKU/service tier.
Nearly every scenario/design question can be structured as "let me walk through this against the 5 pillars" — this is a strong interview framing technique.

**Q26. How do you optimize Azure costs for an enterprise workload?**
- Right-size VMs based on Azure Advisor recommendations.
- Use **Reserved Instances/Savings Plans** for predictable steady-state workloads (up to ~72% savings) and **Spot VMs** for interruptible batch workloads.
- Use **auto-shutdown** for dev/test VMs.
- Move infrequently accessed blobs to **Cool/Archive tier**.
- Use **Azure Hybrid Benefit** to reuse existing on-prem Windows Server/SQL licenses.
- Set **budgets and alerts** via Cost Management + tagging for chargeback/showback.
- Consider **serverless (Functions/Consumption plan)** for spiky/low-traffic workloads instead of always-on VMs.

---

## SECTION 7: MONITORING & DEVOPS

**Q27. What monitoring tools does Azure provide and how do they fit together?**
- **Azure Monitor** — umbrella platform collecting metrics/logs from all resources.
- **Log Analytics** — query engine (KQL) over collected log data.
- **Application Insights** — APM for applications (request rates, dependencies, exceptions, distributed tracing).
- **Azure Advisor** — proactive recommendations (cost, performance, security, reliability).
- **Microsoft Defender for Cloud** — security posture + threat protection.
Typical architecture answer: instrument the app with App Insights SDK, route infra logs/metrics to a Log Analytics workspace, build dashboards/alerts on top, and use Advisor + Defender continuously.

**Q28. How does CI/CD typically work for Azure deployments?**
Azure DevOps or GitHub Actions pipelines: build → run tests → package (container image or artifact) → deploy via IaC (Bicep/Terraform) to Dev → automated tests → approval gate → Staging (deployment slot) → swap/blue-green → Prod, with rollback plans and monitoring gates (e.g., auto-rollback on error-rate spike).

---

## SECTION 8: SCENARIO / REAL-TIME DESIGN QUESTIONS

These are the "design it on a whiteboard" style questions. I've given a framework answer for each — in the interview, always **clarify requirements first** (traffic scale, budget, compliance, RTO/RPO, existing tech stack) before designing.

---

**Scenario 1: "Design a highly available, scalable e-commerce web application on Azure that must survive a full regional outage."**

*Approach:*
- Front Door (global LB + WAF + caching) → routes to App Service (zone-redundant, multi-region: Primary East US, Secondary West US).
- Azure SQL Database with **auto-failover groups** across regions (or Cosmos DB with multi-region writes for the catalog service).
- Redis Cache (zone-redundant) for session/cart data.
- Storage account with **RA-GRS** for product images.
- Service Bus for order processing (decouple checkout from inventory/payment/shipping services).
- CI/CD via Azure DevOps with blue-green deployment slots.
- Application Insights + Log Analytics for monitoring; alerts feeding into an on-call rotation.
- **Failover:** Front Door detects unhealthy region via health probes and reroutes automatically; SQL failover group promotes secondary automatically.

**Scenario 2: "A client wants to migrate a legacy on-prem 3-tier .NET application (IIS + SQL Server + file server) to Azure with minimal re-architecture, then modernize later."**

*Approach (phased "migrate then modernize"):*
- Phase 1 (Lift & Shift): Azure Migrate assessment → IIS servers to Azure VMs (or App Service if compatible) → SQL Server to Azure SQL Managed Instance (near-full compatibility) → file server to Azure Files. Use **Azure Hybrid Benefit** to cut licensing costs. ExpressRoute/VPN for hybrid connectivity during transition.
- Phase 2 (Modernize): move stateless web tier to App Service/AKS, adopt Managed Identity + Key Vault, introduce caching (Redis), break monolith into services incrementally (strangler fig pattern), move SQL MI to Azure SQL DB if feature-compatible.

**Scenario 3: "How would you design a real-time IoT telemetry pipeline processing millions of events/sec, with both real-time alerting and long-term analytics?"**

*Approach:*
- Devices → **IoT Hub** (device management, secure provisioning via DPS) → **Event Hub**-compatible endpoint.
- **Stream Analytics** or **Azure Databricks (Spark Structured Streaming)** for real-time processing — hot path for anomaly detection/alerts (push to Event Grid/Logic Apps for notification).
- Cold path: raw events land in **Data Lake Storage Gen2** (partitioned by date) for batch analytics via Databricks/Synapse.
- Serving layer: aggregated results into **Cosmos DB** (for low-latency dashboards) and **Synapse Analytics** (for BI/Power BI reporting).
- This is the classic **Lambda architecture** (hot + cold path) — naming it explicitly shows architectural maturity in an interview.

**Scenario 4: "Your company has strict compliance requirements — data must never leave a specific region, and access must be tightly audited. How do you design for this?"**

*Approach:*
- Use **Azure Policy** to restrict resource deployment to the approved region only (deny rule for other locations).
- Use **Private Endpoints** for all PaaS services (SQL, Storage, Key Vault) so no traffic traverses the public internet.
- Enable **Microsoft Purview** for data classification/lineage/governance.
- Use **Customer-Managed Keys (CMK)** in Key Vault for encryption at rest (instead of Microsoft-managed keys), giving the customer control/audit over key access.
- Turn on **diagnostic logging + Azure Activity Log** shipped to a Log Analytics workspace with immutable/WORM storage retention for audit trails.
- Use **Conditional Access** policies (Entra ID) to enforce MFA/device compliance for anyone accessing the environment.
- Consider **Azure landing zones** with a management group hierarchy enforcing these policies org-wide, not per-subscription.

**Scenario 5: "An application experiences unpredictable traffic spikes (10x normal load during flash sales) — how do you architect for this cost-effectively?"**

*Approach:*
- App Service/AKS/VMSS with **autoscale rules** based on CPU/queue length, scaling out ahead of predicted spikes (scheduled scaling) plus reactive metric-based scaling.
- Introduce **queue-based load leveling**: front the write-heavy path with Service Bus/Storage Queue so backend workers process at sustainable rate instead of getting overwhelmed synchronously.
- **Cache aggressively** with Redis/CDN (Front Door caching) to reduce backend hits for read-heavy content (product listings).
- Use **Azure SQL DB elastic pool / serverless tier** or Cosmos DB **autoscale throughput (RU/s)** so the database scales with demand instead of being fixed-provisioned.
- Use **Azure Load Testing** ahead of the event to validate the design meets target throughput.
- For genuinely spiky, cost-sensitive batch components, use **Functions Consumption plan** or **Spot VMs** for elastic burst capacity.

**Scenario 6: "How do you design a secure, multi-tenant SaaS application on Azure?"**

*Approach:*
- Decide the **tenancy model** upfront: silo (dedicated resources per tenant — highest isolation/cost), pool (shared resources, logical isolation via tenant ID column/partition key — most cost-efficient), or bridge/hybrid (shared compute, isolated data).
- For pooled data isolation: Cosmos DB partition key = tenant ID, or separate schemas/databases per tenant in Azure SQL elastic pools.
- Use **Entra ID B2B/B2C or External ID** for tenant user authentication, with claims mapping tenant context into JWT tokens, enforced at the API layer.
- APIM as the front door for rate-limiting per tenant (prevent noisy-neighbor issues) and usage metering (for billing).
- Resource tagging per tenant for cost allocation/chargeback.

**Scenario 7: "One of your APIs occasionally times out under load and you suspect a downstream dependency. How do you troubleshoot and prevent future recurrence architecturally?"**

*Approach (troubleshooting → architecture fix):*
- Use **Application Insights dependency tracking / distributed tracing (correlation IDs)** to pinpoint which downstream call is slow.
- Check **Log Analytics** for throttling errors (429s) from the dependency — often indicates hitting a service tier's RU/DTU/connection limit.
- Architectural fixes: introduce a **retry policy with exponential backoff + jitter**, add a **circuit breaker** (e.g., via Polly in .NET) so failures fail fast instead of cascading, and **decouple via a queue** if the dependency is a slow synchronous call that doesn't need to be inline with the user request.
- Scale the dependency tier (e.g., bump Cosmos DB RU/s, DTU tier) or add a **cache layer** in front of it.
- This question is really testing whether you know **resiliency patterns**: retry, circuit breaker, bulkhead isolation, timeout, and queue-based decoupling — name them explicitly.

---

## SECTION 9: QUICK-FIRE RAPID ANSWERS (Common in first-round screens)

- **Q: What's the max storage account size?** → Effectively exabyte-scale per account with per-object/container limits; not usually a hard constraint interviewers expect memorized — focus on the *design pattern* rather than exact numbers.
- **Q: What's a Landing Zone?** → A pre-configured, governed Azure environment (via Azure Policy, Management Groups, networking topology) that new workloads land into, ensuring compliance/security/networking standards from day one — foundational to the **Cloud Adoption Framework (CAF)**.
- **Q: ExpressRoute vs VPN Gateway?** → ExpressRoute = private, dedicated, high-bandwidth, low-latency connection via a connectivity provider (not over public internet) — for mission-critical hybrid workloads. VPN Gateway = encrypted tunnel over public internet — cheaper, easier to set up, higher/variable latency.
- **Q: What is a Bastion host used for?** → Secure RDP/SSH to VMs directly through the Azure portal over TLS, without exposing public IPs/ports on the VM itself.
- **Q: Explain Availability SLA composition.** → Combining SLAs of chained dependent services multiplies (reduces) overall availability (e.g., 99.95% × 99.9% ≈ 99.85% for the whole chain) — important when justifying redundancy investments.

---

## HOW TO STRUCTURE YOUR ANSWERS IN THE INTERVIEW

For any **scenario/design** question, use this flow so you sound structured, not like you're guessing:
1. **Clarify requirements** — scale, budget, compliance, existing stack, RTO/RPO.
2. **Propose high-level architecture** — name the major components/services.
3. **Justify choices** against trade-offs (cost vs performance vs complexity).
4. **Address the "-ilities"** — availability, scalability, security, observability, cost.
5. **Mention how you'd validate it** — load testing, chaos testing, monitoring/alerting.

Good luck with your interview — if you want, I can also turn any of these scenarios into a whiteboard-style architecture diagram, or run a mock Q&A where you answer and I give feedback.
