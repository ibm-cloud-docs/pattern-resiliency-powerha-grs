---

copyright:
  years: 2026
lastupdated: "2026-02-18"

subcollection: pattern-resiliency-powerha-grs

keywords: compute, architecture compute

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}

| Architecture decision | Requirement | Decision | Rationale |
| - | - | - | - |
| Geographic disaster recovery for IBM i workloads | Provide compute capacity for IBM i logical partitions participating in site level disaster recovery | Power Virtual Server LPARs | Supports site level disaster recovery for IBM i workloads using PowerHA SystemMirror for i and Global Replication Service across Power Virtual Server locations. |
| PowerHA SystemMirror for i orchestration | Provide compute for recovery orchestration | Power Virtual Server LPARs | PowerHA runs directly on IBM i logical partitions to manage site roles, Independent Auxiliary Storage Pool activation, and application startup without requiring additional controller systems. |
| Global Replication Service replication | Provide storage replication capability between Power Virtual Server locations | IBM Cloud managed service | Global Replication Service is delivered as a managed service and does not require customer provisioned compute resources. |
| Disaster recovery workload capacity | Provide compute capacity to run production workloads at the recovery site | Power Virtual Server LPARs | Recovery site logical partitions must be sized to support full production workloads during a disaster recovery event. |
| Cost optimized recovery compute | Optimize compute costs for the disaster recovery site | Shared Processor Pools | Shared processor pools provide an optional mechanism to reserve processor capacity at the recovery site while enabling flexible allocation during disaster recovery events. |
| Edge and management services | Provide compute for edge, management, and access services | Virtual Servers for VPC | Virtual servers in edge or management VPCs support administrative and security services and are independent of IBM i disaster recovery operations. |
{: caption="Architecture decisions for compute" caption-side="bottom"}
