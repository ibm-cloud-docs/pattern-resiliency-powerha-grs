---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-pvs-ibmi-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Use Case Requirements

{: #requirements}

This section defines the technical, licensing, and operational requirements to implement the disaster recovery for IBM i on IBM Power Virtual Server using IBM PowerHA SystemMirror for i and Global Replication Services use case.

## Platform requirements
{: #requirements-platform}
Platform requirements
- IBM i 7.5 is installed on all participating IBM i logical partitions.
- IBM i releases and PTF levels are compatible across the primary and recovery locations.
- IBM Power Virtual Server is deployed in two supported locations that are eligible for Global Replication Services.

## PowerHA software requirements
{: #requirements-pha-software}
- IBM PowerHA SystemMirror for i is installed on all participating IBM i logical partitions.
- A PowerHA cluster is configured across the primary and recovery locations.
- Cluster Resource Groups are defined to manage Independent Auxiliary Storage Pool activation and application recovery.
- PowerHA is configured for geographic disaster recovery orchestration only. Local high availability within a single location is not required.

## PowerHA licensing requirements
{: #requirements-licensing}
- IBM PowerHA SystemMirror for i is a separately licensed product and is not included with IBM i by default.
- A valid PowerHA license is required on each participating IBM i logical partition, including both the primary and recovery systems.
- PowerHA licensing is required regardless of whether the recovery system is actively running production workloads.
- PowerHA licensing requirements are the same for IBM Power Virtual Server as for on premises IBM Power Systems.

## Storage requirements
{: #requirements-storage}
- Application data requiring disaster recovery protection resides in an Independent Auxiliary Storage Pool.
- The system Auxiliary Storage Pool remains local to each location and is not replicated.
- IBM Power Virtual Server block storage backed by IBM FlashSystem is used for all Independent Auxiliary Storage Pool volumes.
- Global Replication Services is configured to provide asynchronous replication of all Independent Auxiliary Storage Pool storage volumes between Power Virtual Server locations.
- Storage volume layout and sizing align with supported PowerHA and Global Replication Services limits.

## Compute requirements
{: #requirements-coompute}
- One IBM i logical partition is deployed at the primary location to host the production workload.
- One IBM i logical partition is deployed at the recovery location to assume the production workload during a disaster recovery event.
- The recovery location logical partition is sized to support the full production workload.
- Shared Processor Pools may be used to optimize processor capacity at the recovery location. Shared Processor Pools reserve processor capacity only and do not reserve memory.

## Network requirements
{: #requirements-network}
- Network connectivity exists between Power Virtual Server locations to support PowerHA orchestration, administrative access, and operational coordination.
- Customer managed networks are not used for storage replication traffic.
- Domain Name System configuration supports application access following disaster recovery activation.

## Operational requirements
{: #requirements-operational}
- Disaster recovery procedures are operationally initiated and controlled.
- Procedures exist for failover, failback, and role reversal.
- Regular disaster recovery testing is performed to validate recovery readiness.
- Monitoring and logging are enabled for Power Virtual Server resources, PowerHA cluster events, and Global Replication Services replication status.
