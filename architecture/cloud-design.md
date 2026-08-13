# Cloud-Native Architecture Patterns

**Label:** `Reference Architecture`

## What It Is
Architecture patterns that specifically leverage cloud platform capabilities (managed services, elasticity, pay-per-use) rather than simply lifting-and-shifting traditional on-prem architecture patterns onto cloud infrastructure.

## Why Architects Use It
Cloud platforms enable architectural patterns (true elastic scale, managed PaaS reducing operational burden, consumption-based cost models) that change the calculus of what's practical compared to fixed on-prem capacity — architects should design *for* the cloud, not just *on* it.

## Architecture Considerations
- **Managed services over self-managed infrastructure by default**: prefer PaaS/managed offerings (managed databases, managed Kubernetes) unless a specific requirement justifies the added operational burden of self-managing — matches the compute decision framework in [`azure/compute.md`](../azure/compute.md).
- **Design for elasticity, not fixed capacity**: architect components to scale independently based on actual load (autoscaling groups, serverless, Kubernetes HPA) rather than provisioning for peak capacity permanently.
- **Design for failure**: assume any component can fail at any time — redundancy, retries with backoff, circuit breakers, graceful degradation — cloud infrastructure has different (and sometimes higher-frequency, lower-impact) failure characteristics than traditional on-prem hardware.
- **Loose coupling via managed messaging/queues**: cloud-native managed queue/event services (rather than tightly-coupled synchronous calls) improve resilience and independent scalability between components.
- **Immutable infrastructure**: deploy new versions as new instances/images rather than mutating running infrastructure in place — improves consistency, rollback simplicity, and reduces configuration drift.

## Common Mistakes
- Lift-and-shift architecture (VMs mirroring on-prem server layout) that doesn't leverage elasticity or managed services, capturing little of cloud's actual benefit while still paying cloud's operational learning curve.
- Designing as if infrastructure is reliable/permanent, without retry/backoff and graceful degradation for transient failures that are more common in distributed cloud environments.
- Tight synchronous coupling between components that could be decoupled via a managed queue, propagating failures unnecessarily.

## Principal-Level Questions
- What does "cloud-native" actually mean architecturally, beyond just "running in the cloud"?
- How would you redesign a lift-and-shifted application to actually leverage cloud-native patterns?

## Scenario Question
> A recently "migrated to the cloud" application still runs as a set of always-on VMs sized for peak capacity, with tightly coupled synchronous service calls, and the team is asking why cloud costs are higher than the on-prem equivalent.

## Strong Answer (Outline)
1. Diagnose: this is a lift-and-shift, not a cloud-native redesign — fixed peak-sized VMs pay cloud infrastructure cost without capturing elasticity's cost benefit, which is precisely where cloud's economic advantage comes from for variable-load workloads.
2. Redesign compute: move to autoscaling (VM Scale Sets/managed instance groups, or a PaaS/container platform) so capacity tracks actual load rather than permanent peak provisioning.
3. Decouple tightly-coupled synchronous calls where appropriate via managed queues, improving both resilience and enabling independent scaling of each component based on its own actual load.
4. Adopt managed services where self-managed infrastructure doesn't provide a specific required benefit (e.g., move a self-managed database to a managed offering), reducing operational burden and often improving reliability.
5. Reset expectations: cloud isn't inherently cheaper than on-prem for a fixed, always-on workload — its economic advantage specifically comes from elasticity and reduced operational overhead, which this redesign now actually captures.

## Follow-Up Questions
- How would you sequence this redesign to avoid a risky big-bang rearchitecture of a live production system?
- How do you measure whether the redesign actually achieved the expected cost/resilience improvement?

## Trade-offs
- Cloud-native redesign: captures cloud's real economic and resilience benefits, requires genuine engineering investment and, often, application-level changes (not just infrastructure changes) to fully realize.
