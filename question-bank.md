# Interview Question Bank

**Label:** `Interview Preparation`

A large, difficulty-tiered question bank for self-testing. Questions progress: **Foundation → Intermediate → Senior → Principal → Architecture Scenario**. This is a rapid-fire reference for self-testing — for fully worked answers, see the relevant domain folder and [`scenarios/`](scenarios/).

---

## Azure (50)

**Foundation**
1. What is a management group and why does it exist?
2. What's the difference between Azure RBAC and Azure Policy?
3. What is a managed identity, and why prefer it over a service principal secret?
4. What's the difference between a Network Security Group and Azure Firewall?
5. What is a Private Endpoint, and what problem does it solve?
6. What's the difference between LRS, ZRS, and GRS storage redundancy?
7. What is Azure Policy's "audit" effect versus "deny"?
8. What is PIM, and how does it differ from standing RBAC assignment?
9. What's the difference between Azure Monitor and Log Analytics?
10. What is a hub-spoke network topology?

**Intermediate**
11. When would you use Virtual WAN instead of manual hub-spoke peering?
12. How does a Private Endpoint interact with DNS resolution?
13. Explain the difference between service endpoints and private endpoints.
14. How would you design subscription vending for a growing organization?
15. What's the difference between Azure Firewall and Application Gateway, and when would you use both together?
16. How do you decide when customer-managed keys are justified over platform-managed keys?
17. What causes SNAT port exhaustion, and how do you mitigate it?
18. How would you design a tagging strategy for cost allocation?
19. What's the difference between AKS and Azure Container Apps?
20. How do you handle secrets for a workload that must also run outside Azure?

**Senior**
21. Design a landing zone strategy for a company with 3 business units and strict compliance requirements.
22. How would you migrate 200 ungoverned subscriptions into a governed landing zone without breaking production?
23. Design network segmentation for a PCI-scoped workload on a shared hub-spoke platform.
24. How would you design DR for a 30-minute RTO, 5-minute RPO application?
25. How would you reduce Azure spend by 25% without degrading reliability?
26. Design an observability strategy for 50 microservices with clear on-call ownership.
27. How would you eliminate standing Owner access across an organization without breaking deployments?
28. Design a policy rollout strategy for a new mandatory encryption requirement across 40 subscriptions.
29. How would you design centralized DNS for a hybrid, multi-region environment?
30. What's your approach to preventing accidental public exposure of storage accounts at scale?

**Principal**
31. Design an Azure platform for a healthcare company requiring HIPAA compliance and multi-region DR.
32. How would you integrate two Azure tenants following an acquisition within 6 months?
33. Design identity architecture for a multi-cloud enterprise spanning Azure and GCP.
34. How would you decide the boundary between platform-owned and workload-owned infrastructure?
35. Design a self-service platform for 50 application teams without granting subscription-level access.
36. How would you handle a legacy workload that can't meet a new mandatory security policy?
37. Design a cost governance model giving engineering teams real-time accountability, not a monthly report.
38. How would you design Azure landing zones to support both regulated and unregulated workloads on shared platform services?
39. What's your strategy for managing Key Vault at scale across hundreds of applications?
40. How would you design a break-glass access model that's both secure and usable in a genuine emergency?

**Architecture Scenario**
41. Design a multi-region active-passive architecture for a financial transaction system.
42. Design the full network, identity, and governance architecture for a new enterprise landing zone from scratch.
43. Design a secure, governed self-service platform for provisioning Azure resources across 50 teams.
44. Design an Azure-based data platform (ingestion, storage, compute, governance) for a large-scale analytics workload.
45. Design disaster recovery for a globally distributed e-commerce platform on Azure.
46. Design a zero-trust network architecture for a hybrid Azure/on-prem environment.
47. Design a platform enabling 10 independent product teams to safely share a single Azure environment.
48. Design an Azure Kubernetes platform strategy for an organization moving from VMs to containers.
49. Design a compliant, auditable CI/CD pipeline for infrastructure changes at enterprise scale.
50. Design an incident response and DR runbook for a mission-critical Azure workload.

---

## GCP (50)

**Foundation**
1. What is the GCP resource hierarchy, and how does it compare conceptually to Azure's?
2. What is a Shared VPC, and what problem does it solve?
3. What's the difference between a predefined role and a primitive role in GCP IAM?
4. What is Workload Identity Federation?
5. What's the difference between Cloud NAT and a public IP on a Compute Engine instance?
6. What is Organization Policy, and how does it compare to Azure Policy?
7. What's the difference between GKE Standard and Autopilot?
8. What is VPC Service Controls?
9. What's the difference between Cloud Logging and Cloud Monitoring?
10. What is Private Service Connect?

**Intermediate**
11. Why might a single global VPC be sufficient for a multi-region GCP deployment, unlike Azure's regional VNet model?
12. How would you eliminate downloadable service account keys across an organization?
13. When would you choose Cloud Run over GKE?
14. How does GKE Workload Identity work, and what problem does it solve?
15. What's the difference between Cloud SQL cross-region replicas and Cloud Spanner for DR purposes?
16. How would you design a folder structure for an organization with 3 business units and 3 environments?
17. What's the purpose of IAM Conditions, and when would you use them?
18. How would you enforce data residency using Organization Policy?
19. What's the trade-off between a dedicated project per workload versus fewer, larger projects?
20. How would you design Cloud DNS for a hybrid on-prem/GCP environment?

**Senior**
21. Design a landing zone for a company adopting GCP as a secondary cloud alongside an existing Azure estate.
22. How would you defend a sensitive BigQuery dataset against a compromised but IAM-valid identity?
23. Design a Shared VPC topology for 20 service projects across 3 environments.
24. Your GCP bill spiked due to an inefficient BigQuery query pattern. Diagnose and remediate.
25. How would you migrate 300 downloadable service account keys to Workload Identity Federation?
26. Design multi-region DR for a workload requiring strict consistency.
27. How would you reduce operational burden across 15 GKE Standard clusters?
28. Design an Organization Policy rollout enforcing EU-only data residency.
29. How would you unify GCP and Azure monitoring into a single operational view?
30. What's your approach to preventing GKE clusters from being provisioned with default, insecure settings?

**Principal**
31. Design a GCP landing zone that must interoperate with an existing Azure-centric identity platform.
32. How would you govern a "many small projects" GCP model at 1,000+ project scale?
33. Design a self-service GCP platform allowing teams to provision projects without direct console access.
34. How would you decide between building custom RAG and using Vertex AI's built-in grounding?
35. Design a strategy to eliminate all standing broad IAM roles across a GCP organization.
36. How would you handle a business unit requiring workloads to remain isolated from all others, even via shared services?
37. Design a cost attribution model for a "many small projects" GCP estate.
38. What's your approach to keeping GCP and Azure security posture consistent when owned by separate teams?
39. Design an incident response process for a GCP-specific security event (e.g., an anomalous IAM policy change).
40. How would you plan a phased migration of a legacy, ungoverned GCP estate into a landing zone model?

**Architecture Scenario**
41. Design an enterprise data platform on GCP (ingestion → BigQuery → serving) with governance and cost control.
42. Design a self-service platform for provisioning GCP projects and baseline resources with Organization Policy guardrails.
43. Design multi-region DR for a globally distributed application requiring strict consistency.
44. Design network connectivity for a GCP workload spanning 4 regions with on-prem access and business-unit isolation.
45. Design a defense-in-depth strategy for a sensitive dataset in a multi-tenant GCP environment.
46. Design a GKE-based platform strategy for an organization migrating off Compute Engine VMs.
47. Design an organization-wide strategy to eliminate all downloadable service account keys within one quarter.
48. Design a GCP-native observability strategy spanning 30 projects with minimal noise.
49. Design a compliant CI/CD pipeline for Terraform changes across a large GCP estate.
50. Design an AI platform on Vertex AI serving multiple business units with data isolation.

---

## Kubernetes (40)

**Foundation**
1. What are the main control plane components, and what does each do?
2. What's the difference between a Deployment and a StatefulSet?
3. What is etcd, and why does its health matter?
4. What's the difference between a Service and an Ingress?
5. What is a PersistentVolumeClaim?

**Intermediate**
6. Walk through diagnosing a pod stuck in `Pending`.
7. What's the difference between HPA and VPA, and why shouldn't you always run both together?
8. How does a default-deny NetworkPolicy change cluster behavior?
9. What's the difference between `Delete` and `Retain` reclaim policy, and when would you choose each?
10. Explain what happens end-to-end when you run `kubectl apply` for a Deployment.
11. What's the difference between Ingress and the Gateway API?
12. How would you design resource requests/limits for a workload with unknown traffic patterns?
13. What's the purpose of a PodDisruptionBudget?
14. How does GitOps differ from a traditional CI-push deployment model?
15. What's the difference between a ClusterRole and a Role?

**Senior**
16. A cluster upgrade caused a stateful workload outage despite 3 replicas. Diagnose.
17. Design multi-tenant isolation for 10 teams sharing one cluster.
18. Your Cluster Autoscaler isn't scaling down at night. Diagnose likely causes.
19. Design a GitOps repository structure for 50 applications across 5 environments.
20. How would you diagnose intermittent DNS resolution failures under load?
21. A service scales via HPA but response times keep degrading. Diagnose.
22. How would you design storage for a StatefulSet database across 3 availability zones?
23. Design observability for a platform spanning 20 clusters across 2 clouds.
24. How would you replace cluster-admin CI/CD bindings with least-privilege access?
25. What's your process for validating a backup strategy for stateful Kubernetes workloads actually works?

**Principal**
26. Design a Kubernetes platform strategy for an organization running 40 inconsistent, independently-managed clusters.
27. Propose a GitOps-based deployment model for a 200-microservice platform with strict compliance requirements.
28. A misconfigured PDB blocked node draining during a security patch window, causing an outage. Redesign the upgrade process.
29. How would you decide when a workload warrants its own dedicated cluster versus sharing one?
30. Design a strategy to eliminate all downloadable service account keys and long-lived Kubernetes Secrets holding cloud credentials.

**Architecture Scenario**
31. Design a Kubernetes-based internal developer platform giving 30 teams self-service deployment with centralized guardrails.
32. Design multi-region Kubernetes architecture for a workload requiring a 15-minute RTO.
33. Design a secure, multi-tenant shared cluster architecture from scratch, covering RBAC, networking, and resource fairness.
34. Design a progressive delivery (canary) strategy for a critical production service.
35. Design a disaster recovery strategy for stateful workloads running in Kubernetes.
36. Design an autoscaling strategy for a workload with highly bursty, unpredictable traffic.
37. Design a Kubernetes cost allocation and optimization strategy for a shared cluster serving 15 teams.
38. Design a troubleshooting runbook for the top 5 most common production Kubernetes incidents.
39. Design a Kubernetes platform migration plan from a single large cluster to a hybrid per-environment model.
40. Design the observability, security, and GitOps architecture for a greenfield Kubernetes platform.

---

## Terraform (40)

**Foundation**
1. Walk through the `init` → `plan` → `apply` workflow.
2. Why should Terraform state never be stored locally in a team setting?
3. What's the difference between a resource and a data source?
4. What is state locking, and why does it matter?
5. What's the purpose of provider version pinning?

**Intermediate**
6. A `terraform apply` fails halfway through. How do you recover safely?
7. Design a module structure for a reusable "compliant storage account" pattern.
8. What's the risk of a monolithic Terraform configuration managing an entire estate?
9. How would you handle secrets needed during `plan`/`apply`?
10. What's the purpose of the `moved` block, and when would you use it?

**Senior**
11. When should you use Terraform workspaces versus separate state files/configurations?
12. Terraform state has become corrupted for a critical production stack. Walk through recovery.
13. A module used by 50 stacks needs a breaking change. Plan the rollout.
14. Design a CI/CD pipeline for Terraform balancing safety for production with velocity for lower environments.
15. How would you design least-privilege execution identities across 40 independently-owned stacks?
16. A nightly drift-detection job flags unexpected changes to a production resource. Walk through your response.
17. How would you roll out a new mandatory policy-as-code check without breaking existing pipelines?
18. What's the difference between plan-time policy enforcement and deployed-resource policy enforcement, and why use both?
19. How would you decompose a monolithic Terraform configuration into smaller, safer stacks?
20. How do you prevent secrets from ending up in Terraform state or logs?

**Principal**
21. Design a large-enterprise Terraform architecture spanning 100+ stacks, multiple clouds, and varying team ownership.
22. Design a secure CI/CD pipeline for Terraform safe for junior engineers to use without direct production access.
23. Propose a policy-as-code strategy that prevents non-compliant infrastructure from being created in the first place.
24. A security review finds a single, long-lived Terraform service principal with Owner access tenant-wide. Redesign.
25. How would you migrate an organization's Terraform estate off long-lived static credentials?

**Architecture Scenario**
26. Design the full lifecycle for a new team onboarding onto a Terraform-based self-service platform.
27. Design a Terraform strategy for managing infrastructure consistently across Azure and GCP.
28. Design a module versioning and testing strategy for a platform team supporting 200 internal consumers.
29. Design a drift-detection and remediation strategy for a 500-stack Terraform estate.
30. Design a Terraform-based landing zone provisioning system supporting automated subscription/project vending.
31. Design a disaster recovery plan for Terraform state itself.
32. Design a Terraform CI/CD pipeline enforcing mandatory security review for platform-layer stacks only.
33. Design an approach to migrate 50 stacks from ClickOps/manual management onto Terraform without a risky big-bang cutover.
34. Design a Terraform testing strategy (unit, integration, policy) for a shared module library.
35. Propose a governance model for who can approve changes to shared, high-blast-radius Terraform stacks.
36. Design a strategy for managing Terraform across teams with very different risk tolerances (e.g., a regulated business unit vs. an experimental one).
37. Design an emergency-change process for Terraform-managed infrastructure that bypasses the normal review cycle safely.
38. Design a cost-estimation gate integrated into the Terraform CI/CD pipeline.
39. Design a strategy for safely importing a large existing (non-Terraform-managed) estate into Terraform management.
40. Design the full observability stack for monitoring the health and compliance of a large Terraform-managed estate.

---

## Networking (30)

**Foundation**
1. Explain the OSI model layers relevant to a "site is down" investigation.
2. What's the difference between TCP and UDP?
3. What's the difference between L4 and L7 load balancing?
4. What is a private endpoint, and what problem does it solve?
5. What's the difference between a firewall and a WAF?

**Intermediate**
6. A service works internally but fails intermittently over a site-to-site VPN. Diagnose.
7. How would you design DNS resolution for a hybrid on-prem/multi-cloud environment?
8. What causes SNAT port exhaustion, and how do you fix it?
9. When would you choose ExpressRoute/Interconnect over VPN?
10. What's the difference between a service endpoint and a private endpoint?

**Senior**
11. Design network segmentation for a PCI-scoped workload on a shared hub-spoke platform.
12. Outbound connections intermittently fail under load with connection resets. Diagnose.
13. A single dedicated circuit outage caused a full hybrid connectivity loss. Redesign for resilience.
14. Design health checks to avoid routing traffic to a degraded-but-technically-up instance.
15. How would you safely move a WAF from detection-only to blocking mode in production?

**Principal**
16. Design a global, multi-region load balancing and failover strategy for a latency-sensitive application.
17. Design private-only connectivity for 200 PaaS services, including DNS and policy enforcement.
18. How would you design Zero Trust network segmentation across a hybrid enterprise?
19. Design egress traffic inspection for a hub-spoke network without introducing asymmetric routing.
20. How would you diagnose a "connectivity" issue that turns out to be a DNS problem, and how do you build in faster detection next time?

**Architecture Scenario**
21. Design multi-cloud network connectivity between Azure and GCP for low-latency private service communication.
22. Design the full network architecture for a new enterprise landing zone from scratch.
23. Design network connectivity and segmentation for a workload spanning 4 regions with business-unit isolation.
24. Design a defense-in-depth network architecture for a regulated financial workload.
25. Design a network resilience strategy assuming any single connectivity path can fail.
26. Design DNS architecture for an organization migrating from a single cloud to multi-cloud.
27. Design network observability (flow logs, tracing) for a large hybrid estate.
28. Design a WAF and DDoS protection strategy for a public-facing, high-traffic application.
29. Design a network architecture supporting both public-facing and fully private-only workloads on shared platform infrastructure.
30. Design network connectivity for a newly-acquired company's datacenter into an existing cloud landing zone.

---

## Security (30)

**Foundation**
1. Explain the shared responsibility model and how it differs between IaaS and PaaS.
2. What's the difference between authentication and authorization?
3. What is least privilege, and why does it matter?
4. What's the difference between encryption at rest and encryption in transit?
5. What is Zero Trust, in one sentence?

**Intermediate**
6. A team uses a shared service account with broad permissions for convenience. Redesign their access model.
7. Design a secrets management strategy for a new microservices platform.
8. How would you prevent privilege escalation via role/permission chaining?
9. What's the difference between CSPM and SIEM?
10. How would you decide when customer-managed encryption keys are actually justified?

**Senior**
11. A security audit finds 200+ hardcoded secrets across your application estate. Build a remediation plan.
12. Design a defense-in-depth strategy for a dataset containing regulated customer data.
13. How would you operationalize 3,000 CSPM findings with a 3-person security team?
14. A public-facing storage container was found misconfigured for public access. Respond and remediate.
15. How would you design a SIEM strategy for a multi-cloud environment with disparate logging formats?

**Principal**
16. How would you implement Zero Trust across Azure and GCP consistently, given budget constraints and legacy systems?
17. Design identity architecture for a multi-cloud enterprise with 5,000 employees.
18. How would you prevent a compromised, IAM-valid identity from exfiltrating sensitive data?
19. Design a security architecture for a greenfield fintech platform handling payment data.
20. A newly acquired company must be integrated into your security program within 6 months from an unknown posture. Plan it.

**Architecture Scenario**
21. Design end-to-end security architecture (identity, network, data protection, monitoring) for a new regulated platform.
22. Design a Zero Trust rollout plan for an organization currently relying on perimeter-based trust.
23. Design a vulnerability management program covering infrastructure, containers, and dependencies.
24. Design an incident response process for a suspected credential compromise.
25. Design a security architecture for a shared multi-tenant Kubernetes platform.
26. Design a governance model unifying security posture across two clouds owned by separate teams.
27. Design a secrets elimination strategy migrating an entire estate off long-lived static credentials.
28. Design a security review process for new AI/GenAI feature launches.
29. Design a break-glass access model that's both secure and usable during a genuine emergency.
30. Design a security architecture for protecting a shared vector database serving multiple business units.

---

## Platform Engineering (30)

**Foundation**
1. What's the difference between DevOps and Platform Engineering?
2. What is a "golden path"?
3. What does "platform as a product" mean?
4. What's the difference between self-service and a ticket-based provisioning process?
5. What is an Internal Developer Platform?

**Intermediate**
6. A new microservice currently takes 6 weeks to provision. Redesign the process.
7. How do you decide what belongs in a golden path versus what teams should decide themselves?
8. How would you measure whether a platform investment is improving developer velocity?
9. What's the risk of granting broad standing cloud access to enable self-service?
10. How would you keep a service catalog from becoming stale?

**Senior**
11. Adoption of your golden path is low despite being "the recommended approach." Diagnose and fix.
12. Design a self-service infrastructure platform avoiding direct cloud subscription/project access for requesters.
13. Leadership questions platform team headcount as overhead. Build the business case.
14. How would you decide between adopting an open-source IDP foundation versus building custom?
15. Design a golden path for provisioning a new Kubernetes-based microservice.

**Principal**
16. Design a self-service cloud platform allowing developers to provision compliant infrastructure without direct subscription/project access.
17. Design an internal developer platform strategy for an organization with 200 engineers and no existing golden paths.
18. How would you design a golden path rollout that doesn't become a "golden cage"?
19. Propose a phased, multi-quarter roadmap for building a platform engineering capability from scratch.
20. How would you handle a team with a genuine need to deviate from the golden path?

**Architecture Scenario**
21. Design the end-to-end architecture and governance of a platform allowing 50 teams to self-service provision Kubernetes workloads.
22. Design a developer portal (service catalog) architecture integrating provisioning, CI/CD, and observability.
23. Design a platform team operating model, including roadmap process and success metrics.
24. Design an onboarding golden path for a new application team joining the platform.
25. Design a platform strategy balancing centralized guardrails against team autonomy for 10 business units.
26. Design a self-service database provisioning capability with appropriate guardrails.
27. Design a platform-level cost visibility and chargeback model for self-service infrastructure.
28. Design an exception-handling process for requests outside the standard golden path catalog.
29. Design a platform migration plan moving 30 teams from manual provisioning to full self-service.
30. Design a feedback loop process for continuously improving golden paths based on real usage data.

---

## AI/GenAI (30)

*Always lead with an honest maturity framing per [`ai-genai/README.md`](ai-genai/README.md).*

**Foundation**
1. What's the difference between RAG and fine-tuning?
2. What is prompt injection?
3. What is an AI agent, and how does it differ from a single-turn LLM call?
4. What is MCP, in one sentence?
5. What's the difference between a vector database and a traditional relational database?

**Intermediate**
6. A RAG system returns confidently incorrect answers. Diagnose whether the issue is retrieval or generation.
7. Design a cost-control strategy for a rapidly-growing GenAI feature.
8. What's the difference between an agent and workflow automation, and when would you choose each?
9. How would you decide between a dedicated vector database and a vector extension on an existing database?
10. What's the risk of granting an AI agent broad, unscoped tool access?

**Senior**
11. Design an enterprise RAG platform serving multiple business units with different data sensitivity levels.
12. How would you secure MCP-based AI agents against a compromised or overly-broad server?
13. Design a strategy to monitor and evaluate LLM applications in production, beyond a launch-time evaluation.
14. Design a GenAI feature's infrastructure to be resilient to a model provider outage.
15. How would you diagnose a silent quality regression caused by an upstream model provider update?

**Principal**
16. Design a secure AI platform for multiple business units, covering data governance and cost management.
17. How would you govern AI workloads consistently across Azure and GCP?
18. Design an AI agent platform for a consequential business process, including where human-in-the-loop gates are required.
19. How would you decide when self-hosting an open model becomes cost-justified over a hosted API?
20. Design a defense against indirect prompt injection via retrieved/untrusted content.

**Architecture Scenario**
21. Design an enterprise RAG platform end to end: ingestion, retrieval, generation, access control, evaluation, cost.
22. Design an AI agent platform for autonomous customer support with appropriate human approval gates.
23. Design an AI governance review process for new GenAI feature launches.
24. Design a multi-tenant AI platform with strict per-business-unit data isolation.
25. Design an evaluation framework covering quality, safety, and cost for a production LLM application.
26. Design a fallback/degradation strategy for a GenAI feature during a model provider outage.
27. Design a semantic caching and model-tier-routing strategy to control GenAI costs at scale.
28. Design an MCP server architecture with appropriate least-privilege scoping for an internal AI agent.
29. Design a data governance model controlling what data can enter prompts across an organization.
30. Design an AI platform migration plan moving from a single hosted-API integration to a multi-model, multi-provider architecture.

---

## Architecture / System Design (40)

**Foundation**
1. Design a basic URL-shortening service.
2. What's the difference between horizontal and vertical scaling?
3. What's the difference between availability, reliability, and resilience?
4. What's the difference between HA and DR?
5. What's the difference between synchronous and asynchronous communication?

**Intermediate**
6. Design a rate limiter for a public API with multiple customer tiers.
7. A recently "cloud migrated" application still runs as fixed-capacity VMs with tight coupling. Redesign it.
8. When would you choose a monolith over microservices?
9. When would you choose SQL over NoSQL, and vice versa?
10. What's the role of caching in a system design, and what trade-off does it introduce?

**Senior**
11. Design a notification system reliably delivering across email, SMS, and push with retry/dead-letter handling.
12. Design observability for a platform spanning multiple clouds and Kubernetes clusters.
13. Leadership wants to reduce cloud spend by 25% without degrading reliability. Build the plan.
14. Design DR for a business-critical application with a 30-minute RTO and 5-minute RPO.
15. Design a multi-region, active-active e-commerce platform handling flash-sale spikes.

**Principal**
16. Assess whether the organization should adopt genuine multi-cloud resilience following a competitor's cloud outage.
17. Design an architecture governance model for an organization that grew from 20 to 200 engineers in 18 months.
18. How would you design a system to be resilient by default, assuming any component can fail at any time?
19. How would you decide the right RTO/RPO for a system when the business hasn't specified one?
20. Design a FinOps operating model giving engineering teams real-time cost accountability.

**Architecture Scenario**
21. Design a complete system architecture for a ride-sharing platform's core dispatch system.
22. Design a large-scale, multi-region active-passive architecture for a financial transaction system.
23. Design an event-driven order processing system handling bursty, unpredictable load.
24. Design a global content delivery architecture for a media streaming platform.
25. Design a real-time analytics pipeline processing millions of events per second.
26. Design a multi-tenant SaaS platform with strict tenant isolation requirements.
27. Design a search system supporting both keyword and semantic search over a large document corpus.
28. Design a payments processing system with strict consistency and auditability requirements.
29. Design a global user authentication system supporting SSO across multiple product lines.
30. Design a system for safely rolling out a risky change to a small percentage of production traffic first.
31. Design a data pipeline architecture supporting both batch and real-time processing needs.
32. Design a system architecture for a company migrating from on-prem to cloud within a hard deadline.
33. Design an architecture decision governance process scaling review rigor to decision impact.
34. Design a disaster recovery testing program for an organization that has never tested failover.
35. Design a cost-attribution and chargeback model for a shared multi-tenant platform.
36. Design an architecture supporting both a regulated (PCI) and unregulated business unit on shared infrastructure.
37. Design a system for detecting and responding to anomalous user behavior at scale.
38. Design an architecture for a system requiring 99.99% availability with a modest budget.
39. Design a migration plan moving a monolithic application to a strangler-fig microservices architecture.
40. Design an end-to-end architecture review and standards-setting process for a scaling engineering organization.

---

## FinOps (25)

**Foundation**
1. What is FinOps, and how does it differ from traditional IT cost management?
2. What's the difference between reserved capacity and pay-as-you-go pricing?
3. Why does consistent tagging/labeling matter for cost management?
4. What is rightsizing?
5. What's a common source of "hidden" cloud cost that's easy to overlook?

**Intermediate**
6. How would you identify and remediate idle/orphaned cloud resources at scale?
7. When is reserved/committed capacity worth the trade-off in flexibility?
8. How would you design Kubernetes cost allocation for a shared cluster serving multiple teams?
9. What's distinctive about controlling costs for a GenAI/LLM-based feature versus traditional compute?
10. How would you build a business case for a FinOps investment to a skeptical leadership team?

**Senior**
11. Leadership wants to reduce cloud spend by 25% without degrading reliability. Build the plan.
12. A team's cloud costs grew 3x while workload only grew 1.5x. Diagnose and remediate.
13. How would you design budget alerting that catches cost growth trends before they become a crisis?
14. Design a cost-control strategy for a rapidly-growing GenAI feature.
15. How would you handle a team resisting rightsizing due to a past capacity-related incident?

**Principal**
16. Design a FinOps operating model giving engineering teams real-time cost visibility and accountability.
17. How would you balance aggressive cost optimization against genuine headroom for unpredictable growth?
18. Design a chargeback/showback model for a shared multi-tenant platform.
19. How would you build sustained cost discipline that doesn't erode a few months after an initial cost-cutting push?
20. Design a cost governance model spanning multiple clouds with different native cost-management tooling.

**Architecture Scenario**
21. Design a complete FinOps program from scratch for an organization with no current cost visibility.
22. Design a cost-optimization initiative targeting a Kubernetes platform's disproportionate cost growth.
23. Design a token-usage and cost-attribution system for a multi-tenant AI platform.
24. Design a reserved-capacity strategy for a mix of steady-state and highly variable workloads.
25. Design an automated cost-anomaly detection and alerting system across a multi-cloud estate.

---

## Leadership / Behavioral (30)

See [`leadership/`](leadership/) and [`docs/behavioral-framework.md`](docs/behavioral-framework.md) for frameworks and worked examples.

**Foundation**
1. Tell me about yourself and your architecture background.
2. Why are you interested in this role?
3. Describe your ideal working relationship with a product manager.
4. What does "principal architect" mean to you, in your own words?
5. How do you stay current with cloud/architecture trends?

**Intermediate**
6. Tell me about a time you disagreed with a technical decision.
7. Tell me about a time you had to influence without direct authority.
8. Describe a time you had to simplify an overly complex design.
9. Tell me about a time you had to say no to a stakeholder.
10. Describe a time you mentored someone technically.

**Senior**
11. Tell me about a production incident you led or contributed to resolving.
12. Describe a time a decision you made turned out wrong. What did you do next?
13. Tell me about a time you had to make a decision with incomplete information.
14. Describe a time you had to drive a standard or governance change across resistant teams.
15. Tell me about a time you had to balance competing priorities between security and velocity.

**Principal**
16. How do you scale your technical judgment across an organization larger than you can personally review?
17. Describe a time you changed a previously-stated technical position based on new information.
18. How do you decide when a disagreement needs escalation versus resolution at your own level?
19. Tell me about a time you had to communicate a complex technical trade-off to a non-technical executive.
20. How do you approach technical debt at an organizational level?

**Reflective / Open-Ended**
21. How do you build trust with a new team you're joining as an architect?
22. What's your philosophy on when to standardize versus when to allow team autonomy?
23. How do you handle being wrong in front of a team you're supposed to be leading technically?
24. What's the hardest part of being a principal architect, in your experience?
25. How do you balance being opinionated with staying open to being persuaded?
26. Describe your approach to giving critical feedback on someone's architecture.
27. How do you decide what to say no to, given limited time and broad scope?
28. What's a technical opinion you hold that many of your peers would disagree with?
29. How do you approach onboarding into an unfamiliar, legacy-heavy technical environment?
30. What would your peers say is your biggest growth area as an architect?

---

## Scenario Questions (50)

Cross-domain scenarios pulling from multiple areas — the kind typically asked in the final loop round. For fully worked examples, see [`scenarios/`](scenarios/).

**Cloud Architecture**
1. Design an enterprise Azure platform for a company with 5 business units.
2. Design a GCP enterprise foundation for a company adopting GCP as a secondary cloud.
3. Design a multi-cloud platform balancing consistency and best-of-breed service usage.
4. Design hybrid cloud connectivity for a company with a large on-prem footprint.
5. Design secure cloud landing zones supporting both regulated and unregulated workloads.

**Kubernetes**
6. Diagnose and resolve a production cluster outage during a routine upgrade.
7. Diagnose an API server that has become slow to respond under normal workload volume.
8. Design a plan to handle Kubernetes cluster capacity exhaustion during a traffic spike.
9. Diagnose an application networking failure affecting only cross-namespace traffic.
10. Design a multi-region Kubernetes architecture for regional failover.

**Terraform**
11. Diagnose and recover from Terraform state corruption on a critical production stack.
12. Diagnose unexpected drift on a production resource flagged by a nightly detection job.
13. Plan a rollout for a breaking module dependency change affecting 50 consuming stacks.
14. Design a large-enterprise Terraform architecture from scratch.
15. Design a secure CI/CD pipeline for infrastructure changes.

**Security**
16. Respond to a report of compromised credentials for a broadly-privileged identity.
17. Redesign access for a team with excessive standing privileges.
18. Respond to a public storage exposure incident.
19. Respond to a security policy violation discovered during a routine audit.
20. Respond to a gradual cloud security posture degradation flagged by CSPM trend data.

**FinOps**
21. Diagnose a sudden, unexplained cloud bill increase.
22. Diagnose disproportionately high Kubernetes costs relative to workload growth.
23. Diagnose an AI workload that has become unexpectedly expensive.
24. Build a plan to reduce cloud spend by 25% without degrading reliability.
25. Design ongoing cost governance to prevent a repeat of a past cost-growth incident.

**AI**
26. Design an enterprise RAG architecture for internal knowledge management.
27. Design an AI agent platform for a consequential business process.
28. Design an enterprise MCP architecture with appropriate security scoping.
29. Design an AI governance platform covering approval, evaluation, and monitoring.
30. Design an LLM observability strategy for a production GenAI feature.

**Cross-Domain / Composite**
31. A newly acquired subsidiary has 40 ungoverned Azure subscriptions. Bring it under governance within two quarters.
32. Design a self-service platform allowing 50 teams to provision compliant infrastructure without direct cloud access.
33. Design a platform strategy for an organization moving from 40 inconsistent Kubernetes clusters to a governed model.
34. Design a security architecture for a greenfield fintech platform handling regulated payment data.
35. Design an architecture governance model for a company that grew from 20 to 200 engineers rapidly.
36. Following an acquisition, integrate an Azure-centric org with a GCP-centric acquired company within 12 months.
37. Design a secure AI platform for multiple business units with different data sensitivity levels.
38. Diagnose a production outage that occurred during a routine, low-risk-seeming change.
39. Design DR for a business-critical application with strict RTO/RPO requirements.
40. Design an internal developer platform strategy for an organization with no existing golden paths.
41. Respond to an AI agent that executed an unintended action after processing an untrusted document.
42. Design a defense-in-depth strategy for a shared vector database serving multiple business units.
43. Design a multi-region, active-active architecture for a latency-sensitive customer-facing application.
44. Design an incident response and communication process for a major customer-facing outage.
45. Design a phased plan to migrate 300 on-prem applications to the cloud within an 18-month deadline.
46. Design a golden-path rollout strategy for an organization where adoption of the previous attempt was low.
47. Design a Zero Trust rollout plan for a large enterprise with significant legacy system dependencies.
48. Design a unified observability strategy across two clouds and 20 Kubernetes clusters.
49. Design a platform allowing safe, self-service provisioning of GenAI features across business units.
50. Design a full architecture review and technical leadership plan for your first 90 days as a newly-hired Principal Architect.
