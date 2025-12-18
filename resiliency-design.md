---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Resiliency design
{: #resiliency-design}

Resiliency is the ability of a workload to continue to meet defined service level objectives and recover from service disruptions while maintaining required availability targets. In this pattern, resiliency is implemented at the infrastructure and operating system orchestration layers to support site level recovery of IBM i workloads running on IBM Power Virtual Server.

This pattern focuses on geographic resiliency, sometimes informally referred to as global high availability, by enabling controlled recovery of workloads between geographically separated Power Virtual Server locations. Local operating system high availability within a single location is not a primary objective of this design.

Resiliency needs to be considered for both the infrastructure and application levels. This pattern does not address application or database-specific design.
{: note}

It's important to validate what offerings are available in the regions you are deploying. Check for paired data centers and validate if they meet the deployment criteria for client-specific requirements.

## Resiliency objectives
{: #resiliency-objectives}

The resiliency objectives for this pattern are to enable recovery of IBM i workloads following the loss of a Power Virtual Server location while meeting defined recovery objectives. Specifically, this pattern is designed to support:
- Site level failover of IBM i workloads to a recovery location in a different region
- Recovery that meets defined Recovery Time Objectives and Recovery Point Objectives based on asynchronous replication behavior

Resiliency needs to be considered for both the infrastructure and application levels. This pattern does not address application or database-specific design.
{: note}

## Role of PowerHA SystemMirror for i
{: #role-pha}

This pattern implements a geographic disaster recovery model using two Power Virtual Server locations. A primary location hosts the active IBM i workload, while a secondary location is designated as the recovery site. Only one location is active at any time.

Application data is placed in an Independent Auxiliary Storage Pool, which enables controlled activation of data at the recovery site. Storage replication between locations is performed using Global Replication Services, which provides asynchronous storage replication at the Power Virtual Server storage layer.

![Standard PHA](/images/standardpha.svg "PowerHA geographic mirroring diagram"){: caption="Figure 2: PowerHA geographic mirroring architecture" caption-side="bottom"}{: external download="standardpha.svg"}

## Role of Global Replication Services
{: #role-grs}

Global Replication Services provides asynchronous storage replication between paired Power Virtual Server locations. Replication operates at the storage layer and is independent of the IBM i operating system.

Replication is performed using consistency group technology to maintain write order across replicated volumes. Because replication is asynchronous, some data loss may occur depending on replication lag at the time of failure. The effective recovery point is determined by the most recent completed consistency group.

Replication traffic is managed by the Power Virtual Server service and does not traverse customer managed networks.

## Resiliency design considerations for disaster recovery
{: #dr-design}

The Power Systems Virtual Server service provides a Tier 2 99.95% SLA by default. When a Logical Partition (LPAR) has an outage within the service, it automatically attempts to restart that LPAR on a separate host. For a Tier 3 SLA of 99.99%, the workload is distributed across two data centers. Supporting a 1 hour RTO and 1 hour RPO, the solution that is described in this pattern includes:

- A secondary data center
- SAN to SAN replication paired between two {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}} (PowerVS) workspaces in different data centers.
- Global Replication services (GRS) are based on industry standards {{site.data.keyword.IBM_notm}} Storwize Global Mirror Change Volume Asynchronous replication technology. For more information, see [getting started with GRS](/docs/power-iaas?topic=power-iaas-getting-started-GRS).

The GRS involves two sites, over 300 km apart, where storage replication is enabled. These two sites are fixed and mapped into a one-to-one relationship mode in both directions. These two sites are fixed and are in replication partnership in both directions. You can create a replication-enabled volume from any site, the site from where the request is initiated contains the main volume and play the role of primary. The remote site is auxiliary and contains an auxiliary volume.

Review the key capabilities for disaster recovery design considerations:

- Volume-based async storage replication that uses Consistency Groups (CG).
- CG Services to create and delete CGs, add and remove volumes, stop and start, and so on.
- Volume services to create, delete, and retype enabled to support mirrored volumes.
- Virtual machines services to deploy, delete, attach, detach, clone, and snapshot for mirrored volumes.

When a write operation is issued to a source volume, the changes are typically propagated to the target volume a few seconds after the data is written to the source volume. However, changes can occur on the source volume before the target volume verifies that it received the change. Because consistent copies of data are formed on the secondary site at set intervals, data loss is determined by the amount of time since the last consistency group was formed. If the system fails, Global Mirror might lose some data that was transmitted when the failure occurred.

For more information, see [Global Replication Services Solution using {{site.data.keyword.IBM_notm}} {{site.data.keyword.powerSys_notm}}](https://cloud.ibm.com/media/docs/downloads/power-iaas/Global_Replication_Services_Solution_using_IBM_Power_Virtual_Server.pdf){: external}. You can also review the [locations](/docs/power-iaas?topic=power-iaas-getting-started-GRS) that support a global replication service.

![GRS](/images/grs.svg "GRS Diagram"){: caption="Figure 3: GRS Architecture" caption-side="bottom"}{: external download="grs.svg"}
