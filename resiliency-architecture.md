---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

# Architecture decisions for resiliency
{: #resiliency-architecture}

The following sections summarize the resiliency architecture decisions for workloads deployed on {{site.data.keyword.vpc_short}} infrastructure.

## Resiliency architecture decisions

| Architecture decision | Requirement | Decision | Rationale |
|---|---|---|---|
| Resiliency scope | Protect IBM i workloads from site-level failures | Geographic disaster recovery | This pattern is designed to recover from the loss of an entire Power Virtual Server location and does not provide local high availability. |
| Resiliency model | Define recovery behavior across locations | Active–passive site model | Only one site is active at any time. Applications are recovered at the secondary site during a disaster event. |
| Data replication method | Replicate application data between sites | Global Replication Services | Global Replication Services provides asynchronous storage replication at the PowerVS storage layer without requiring customer-managed compute resources. |
| Recovery orchestration | Coordinate failover and failback operations | PowerHA SystemMirror for i | PowerHA manages site roles, IASP activation, and application startup sequencing during recovery. |
| Data isolation | Enable controlled activation of application data | Independent Auxiliary Storage Pool | IASP isolates application data and is required to support recovery at the secondary site using PowerHA and GRS. |
| Recovery initiation | Control when recovery occurs | Manual, operationally initiated | Recovery actions are initiated by operations staff to avoid unintended site role changes. |
| Recovery objectives | Define expected recovery outcomes | RPO ≈ 15 minutes, RTO ≈ 1 hour | Recovery objectives align with asynchronous replication behavior and operational recovery processes. |
{: caption="Resiliency architecture decisions" caption-side="bottom"}
