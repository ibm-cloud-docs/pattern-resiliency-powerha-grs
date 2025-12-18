---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Use case: Disaster recovery for IBM i using PowerHA SystemMirror for i and Global Replication Services
{: #dr-usecase}

This use case describes a site level disaster recovery solution for IBM i workloads running on IBM Power Virtual Server using IBM PowerHA SystemMirror for i in combination with Global Replication Services. The solution protects IBM i environments from data center level outages while leveraging IBM Cloud managed storage replication and IBM supported recovery orchestration.

The solution is implemented as a disaster recovery only architecture. It does not provide local high availability within a single Power Virtual Server location and does not include backup, archival storage, or long term data retention capabilities.

## Business context
{: #usecase-business-context}

Organizations running mission critical IBM i workloads require protection from regional outages, infrastructure failures, or planned site evacuations. When deploying IBM i on Power Virtual Server, customers often require a disaster recovery solution that integrates cleanly with IBM Cloud services, avoids complex host based replication, and provides predictable recovery behavior.

This use case addresses those requirements by combining storage level asynchronous replication with operating system level orchestration, using IBM supported technologies that clearly separate replication from recovery logic.

## Solution overview
{: #usecase-solution-overview}

This use case combines two complementary technologies, each with a distinct role.

Global Replication Services provides asynchronous replication of Power Virtual Server storage volumes between two geographically separated Power Virtual Server locations. Replication is delivered as a managed IBM Cloud service, operates at the Power Virtual Server storage layer, and is transparent to the IBM i operating system. Storage replication does not use customer managed networks.

IBM PowerHA SystemMirror for i orchestrates disaster recovery operations. PowerHA does not replicate data. Instead, it coordinates site role changes, activates replicated storage, manages Independent Auxiliary Storage Pools, and controls application startup sequencing at the recovery site.

This separation of responsibilities aligns with IBM and Fortra best practices and simplifies operational recovery.


## Architecture and deployment model
{: #usecase-architecture-deployment}

The solution is deployed across two Power Virtual Server locations.

The primary site hosts an IBM i logical partition running the active production workload. Application data resides in an Independent Auxiliary Storage Pool on Power Virtual Server storage backed by IBM FlashSystem.

The recovery site hosts a corresponding IBM i logical partition that remains inactive or lightly utilized during normal operations. This logical partition is provisioned to assume the full production workload during a disaster recovery event.

Compute capacity at the recovery site must be sufficient to support production workloads. Shared Processor Pools may be used to optimize processor cost, with the understanding that Shared Processor Pools reserve processor capacity only and do not reserve memory.

## Role of Independent Auxiliary Storage Pools
{: #usecase-iasp}

All application data requiring disaster recovery protection resides in an Independent Auxiliary Storage Pool. The system Auxiliary Storage Pool, which contains the IBM i operating system and licensed internal code, remains local to each site and is not replicated.

Independent Auxiliary Storage Pools are mandatory because they enable isolation of application data from the operating system, independent vary on and vary off operations, and geographic activation of application data at the recovery site under PowerHA control.

Without an Independent Auxiliary Storage Pool, this use case cannot be implemented.

## Disaster recovery orchestration with PowerHA
{: #usecase-pha-orchestration}

IBM PowerHA SystemMirror for i manages disaster recovery using Cluster Resource Groups configured for geographic recovery. Cluster Resource Groups define the relationships between nodes, Independent Auxiliary Storage Pools, and application resources and control the sequence of recovery operations.

During a disaster recovery event, recovery is operationally initiated, PowerHA coordinates the site role transition, Global Replication Services replicated Independent Auxiliary Storage Pool volumes are activated at the recovery site, the Independent Auxiliary Storage Pool is varied on at the recovery site IBM i logical partition, applications defined within the Cluster Resource Group are started in a controlled order, and user access is redirected to the recovery site.

Failback operations follow a similarly controlled process once the primary site is restored and replication is re established.


## Networking and orchestration assumptions
{: #usecase-pha-orchestration}

Network connectivity between Power Virtual Server locations is required to support PowerHA orchestration, administrative access, and operational coordination. Customer managed networks are not used for storage replication traffic. All replication is handled internally by Global Replication Services within the Power Virtual Server service infrastructure.

No controller or quorum logical partitions are required. PowerHA runs directly on the IBM i logical partitions, while Global Replication Services independently manages storage replication.


## Applicability
{: #usecase-applicability}

This use case is appropriate for customers who require site level disaster recovery for IBM i workloads, asynchronous replication with minute level data loss tolerance, IBM Cloud managed storage replication, PowerHA based recovery orchestration using Cluster Resource Groups, and a disaster recovery solution without local high availability.

This use case is not appropriate for environments requiring zero data loss, sub minute recovery times, active active architectures, or workloads that cannot be placed in an Independent Auxiliary Storage Pool.

## Summary
{: #usecase-summary}

This use case demonstrates how IBM PowerHA SystemMirror for i and Global Replication Services can be combined on IBM Power Virtual Server to deliver a robust, supportable disaster recovery solution for IBM i workloads. By leveraging Independent Auxiliary Storage Pools, Cluster Resource Group based orchestration, and IBM Cloud managed storage replication, the solution provides predictable recovery behavior while aligning with IBM Cloud and Fortra best practices.
