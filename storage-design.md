---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}

This section describes the storage design considerations for a disaster recovery–only deployment of IBM PowerHA SystemMirror for i with Global Replication Services (GRS) on IBM Power Virtual Server (PowerVS). The design focuses exclusively on storage constructs required to support geographic disaster recovery and does not include backup or long-term data retention.

## IBM Power Virtual Server storage
{: #pvs-storage-considerations}

IBM Power Virtual Server provides native block storage services backed by IBM FlashSystem infrastructure. Storage is provisioned as part of the PowerVS workspace and presented to IBM i logical partitions (LPARs) as disk units. Customers do not directly manage FlashSystem resources.

PowerVS storage supports Global Replication Services (GRS), which enables asynchronous replication of storage volumes between supported PowerVS locations. GRS is a foundational component of the disaster recovery design described in this pattern.

## IBM i storage constructs
{: #storage-constructs}

IBM i uses Auxiliary Storage Pools (ASPs) to organize disk storage. There are two relevant storage constructs in this pattern:
- System Auxillary Storage Pool (ASP): Contains the IBM i operating system, licensed internal code, and system objects
- Independent Auxiliary Storage Pool (IASP): Contains application data and can be managed independently of the system ASP

In this pattern, only IASPs are used for disaster recovery.

## Independent Auxiliary Storage Pools (IASPs)
{: #storage-iasp}

An Independent Auxiliary Storage Pool (IASP) enables application data to be isolated from the system ASP and managed independently. IASPs can be varied on and off without impacting the IBM i operating system and can be switched between systems during recovery operations.

All application data that requires disaster recovery protection must reside in an IASP. The system ASP remains local to each PowerVS location and is not replicated.

## Why is IASP mandatory? 
{: #storage-iasp-why}

IASPs are mandatory for this pattern for the following reasons:
- Geographic switching

PowerHA SystemMirror for i uses IASPs to perform geographic switching during disaster recovery events.

- Independent activation

IASPs can be varied on at the recovery site without replicating or restarting the IBM i operating system.

- Storage-level replication support

Global Replication Services replicates the PowerVS storage volumes that comprise the IASP. System ASP storage is not supported for GRS-based recovery.

- Predictable recovery workflows

IASPs allow PowerHA to coordinate role changes, IASP activation, and application startup in a controlled and repeatable manner.

## Storage replication with Global Replication Services 
{: #storage-replication-grs}

Global Replication Services provides asynchronous replication of PowerVS storage volumes between supported PowerVS locations. In this pattern:
- All storage volumes that make up the IASP are configured for GRS replication
- Replication is transparent to IBM i
- Replication lag at the time of a disaster determines the achievable recovery point objective (RPO)
- Replicated volumes must be activated at the recovery site prior to IASP vary-on

GRS does not provide synchronous replication and does not guarantee zero data loss.

## PowerHA Interaction with storage
{: #storage-powerha-interaction}

IBM PowerHA SystemMirror for i coordinates disaster recovery operations by managing the activation and role transition of replicated storage but does not perform data replication itself. In this pattern, PowerHA assumes that the storage volumes that comprise the Independent Auxiliary Storage Pool (IASP) have been replicated to the recovery site using Global Replication Services (GRS). During a planned or unplanned disaster recovery event, PowerHA orchestrates the recovery workflow by coordinating site role changes, activating the replicated IASP volumes at the recovery location, varying on the IASP, and managing the orderly startup of dependent applications. This separation of responsibilities ensures that storage replication and operating system–level recovery remain independent while enabling consistent and repeatable recovery procedures.

## Recovery objectives
{: #storage-recovery-objectives}

This storage design is intended to target a recovery point objective (RPO) of 15 minutes and a recovery time objective (RTO) of 1 hour. These values represent design objectives and are not guaranteed. Actual recovery outcomes depend on several factors, including application write rates, IASP size and volume layout, network latency and bandwidth between PowerVS locations, replication backlog at the time of failure, and the maturity of operational procedures. Regular testing and validation of disaster recovery workflows are required to confirm that recovery objectives can be achieved consistently.
