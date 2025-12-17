---

copyright:
  years: 2025
lastupdated: "2025-12-17"

subcollection: pattern-pvs-ibmi-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #compute-design}

This section describes the compute design considerations for a disaster recovery–only deployment of IBM PowerHA SystemMirror for i with Global Replication Services (GRS) on IBM Power Virtual Server (PowerVS). The following compute design topics are covered:
- Power Virtual Server compute overview
- IBM i LPAR design
- Disaster recovery considerations
- Capacity planning considerations

## IBM Power Virtual Server compute overview
{: #design-pvs-overview}

IBM Power Virtual Server provides on-demand IBM Power Systems capacity in IBM Cloud, enabling IBM i workloads to run on logical partitions (LPARs) with configurable processor and memory resources. PowerVS supports flexible sizing to accommodate both steady-state production workloads and disaster recovery scenarios.

In this pattern, compute resources are provisioned in two PowerVS locations: a primary location hosting the production workload and a secondary location designated for disaster recovery.

## IBM i LPAR design
{: #design-lpar-design}

Each PowerVS location hosts an IBM i LPAR configured to participate in the PowerHA SystemMirror for i cluster. The primary-site LPAR runs the active production workload, while the recovery-site LPAR remains inactive or lightly utilized until a disaster recovery event occurs.

The recovery-site LPAR must be sized to support the full production workload upon activation. Processor, memory, and operating system configuration should be equivalent or compatible with the primary-site LPAR to ensure predictable recovery behavior.



## Compute considerations for disaster recovery
{: #design-considerations-dr}

This pattern provides site-level disaster recovery for IBM i workloads on IBM Power Virtual Server (PowerVS) using IBM PowerHA SystemMirror for i in combination with Global Replication Services (GRS). No local high availability is provided within a single PowerVS location.

Global Replication Services (GRS) provides asynchronous storage replication at the PowerVS storage layer and is delivered as a managed IBM Cloud service. GRS does not require dedicated compute resources or controller LPARs. Storage replication is handled by the Global Replication Services infrastructure and is transparent to the IBM i operating system. IBM PowerHA SystemMirror for i does not replicate data; instead, it orchestrates disaster recovery actions assuming that GRS-replicated storage is available at the recovery site.

Compute resources are required only for the IBM i LPARs participating in the disaster recovery configuration. One IBM i LPAR is deployed at the primary site to run the production workload, and a corresponding IBM i LPAR is deployed at the recovery site to assume the production role during a disaster recovery event. The recovery-site LPAR can remain inactive or lightly utilized until recovery is initiated.

The recovery-site LPAR must be sized to support the full production workload when activated. Processor and memory capacity should be planned based on peak workload requirements during recovery operations. Operating system versions and PTF levels must be compatible across sites to ensure predictable recovery behavior.

Shared Processor Pools (SPPs) can be used to optimize processor utilization for disaster recovery IBM i LPARs in this pattern. SPPs allow processor capacity to be reserved and shared among PowerVS LPARs while minimizing steady-state compute costs at the recovery site. Shared Processor Pools reserve processor capacity only and do not reserve memory. Memory must be fully provisioned on the IBM i LPAR prior to recovery. During a disaster recovery event, processor entitlement can be increased to support production workload execution.

During a disaster recovery event, PowerHA SystemMirror for i coordinates the activation of the recovery-site LPAR, the vary-on of the replicated Independent Auxiliary Storage Pool (IASP), and the orderly startup of applications. All storage replication required for this process is provided by Global Replication Services, independent of compute resources.

This pattern does not require controller or quorum LPARs. All disaster recovery orchestration is performed directly by PowerHA SystemMirror for i running on the IBM i LPARs, while Global Replication Services independently manages storage replication at the PowerVS infrastructure layer.

The shared processor pool (SPP) reserves only compute capacity, not the memory. For more information, see [The shared processor pool](/docs/power-iaas?topic=power-iaas-manage-SPP) and in [Managing shared processor pools](https://www.ibm.com/docs/en/power9?topic=systems-managing-shared-processor-pools){: external}. For more information on the Global Replication Service, see [GRS](/docs/power-iaas?topic=power-iaas-getting-started-GRS)

## Capacity planning considerations
{: #design-capacity}

When designing compute capacity for this pattern, consider processor and memory requirements necessary to support peak production workloads during recovery operations. The recovery-site LPAR must be capable of assuming the full production workload, and compute sizing should reflect expected utilization during disaster recovery scenarios.

Operating system versions, PTF levels, and application configurations must be compatible across sites. Disaster recovery testing should be performed regularly to validate that compute capacity at the recovery site meets recovery objectives.
