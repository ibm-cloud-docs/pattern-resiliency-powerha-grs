---

copyright:
  years: 2025
lastupdated: "2025-12-30"

subcollection: pattern-resiliency-powerha-grs

keywords:

authors:
- name: Jose Ocasio

version: 1.0

deployment-url:

docs: /docs/pattern-resiliency-powerha-grs

content-type: reference-architecture

---

{{site.data.keyword.attribute-definition-list}}

# IBM i Disaster Recovery with PowerHA and Global Replication Services on PowerVS
{: #power-virtual-server-on-ibmi}
{: toc-content-type="reference-architecture"}
{: toc-version="1.0"}

This is a baseline solution pattern that captures the key design and architecture decisions for implementing IBM PowerHA SystemMirror with Global Replication Services (GRS) on IBM Power Virtual Server (PowerVS) to support high availability and disaster recovery for IBM i workloads. The pattern addresses common resiliency requirements such as local high availability, geographic disaster recovery, and controlled failover across PowerVS locations.

Actual customer implementations may vary based on workload characteristics, recovery objectives, regional availability, and operational requirements. Review the following decision tree to understand how this reference architecture aligns PowerHA and GRS capabilities with typical IBM i resiliency use cases in PowerVS environments.

![Solution Decision Tree](/images/phagrsdtree.svg "Reference Summary"){: caption="Decision tree illustrating IBM i disaster recovery and resiliency options on PowerVS, including PowerHA SystemMirror and Global Replication Services (GRS)." caption-side="bottom"}{: external download="phagrsdtree.svg"}

## Architecture diagram
{: #architecture-diagram}

![IBM PowerHA SystemMirror and Global Replication Services on PowerVS reference architecture](/images/phagrspvsarchnumbered.svg "Resiliency Architecture Diagram"){: caption="Deploying PowerHA and GRS on {{site.data.keyword.powerSysFull}} reference architecture" caption-side="bottom"}{: external download="resiliencypvsarchnumbered.svg"}

Review the environments that are related to this reference architecture:

- Provider connects the environment by using a direct link for private connectivity.
- The direct link then connects to a Local Transit Gateway. This advertises and routes on-premises traffic to VPC for gateway or firewall inspection.
- The transit gateway connects to management VPC, which hosts your Next-Generation Firewall, management subnets for your bastion hosts, and your Virtual Private Endpoint.
- A Virtual Private Endpoint is deployed to the Cloud Services Layer: Cloud Object storage.
- PowerVS workspace is deployed within the {{site.data.keyword.powerSysFull}} environment and connects to the Power Edge Router (PER).
- A Power high availability cluster is then deployed within the workspace to provide DR clustering and orchestration. It is utilizing PowerHA SystemMirror for i as the software and Global Replication Services as the replication method. This allows the replication of storage volumes in Independant Auxillary Storage Pools (IASP)
- The management and workload VPC mentioned from the primary site is also deployed in disaster recovery.
- Communication for GRS SAN to SAN traffic between sites occurs over the {{site.data.keyword.IBM_notm}} private backbone.

## Design scope
{: #design-scope}

The PowerVS resiliency for IBM i workloads architecture covers design considerations and architecture decisions for the following aspects and domains:

- Compute: Virtual Servers

- Storage: Primary Storage

- Networking: Enterprise Connectivity, BYOIP/Edge gateways, Segmentation, Isolation, Cloud Native Connectivity, Domain name system

- Security: Data security, Identity and access management, Infrastructure and endpoint security

- Resiliency: High Availability, Disaster Recovery

The Architecture Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. For more information, see [Introduction to the Architecture Design Framework](/docs/architecture-framework?topic=architecture-framework-intro).

Following the Architecture Design Framework, Resiliency for PowerVS covers design considerations and architecture decisions for the following aspects and domains:

![heatmap](/images/ibmiphagrsdrheatmap.svg "IBM i Heatmap"){: caption="IBM PowerHA SystemMirror for i and GRS on PowerVS Heat Map" caption-side="bottom"}{: external download="ibmiphagrsdrheatmap.svg"}

## Requirements
{: #requirements-list}

| Aspect | Requirements |
| - | - |
| Compute | Provision sufficient PowerVS compute resources (CPU and memory) to support IBM i LPARs and PowerHA SystemMirror for i at both the primary and disaster recovery locations. Compute sizing at the recovery site must support full production workload activation during a disaster recovery event. |
| Storage | Use IBM Power Virtual Server storage, backed by IBM FlashSystem, to host Independent Auxiliary Storage Pools (IASPs). Storage must support Global Replication Services (GRS) asynchronous replication between PowerVS locations and meet application performance and capacity requirements. Storage configuration must align with PowerHA for i and GRS limits. |
| Networking | Provide reliable, private IP connectivity between PowerVS locations to support PowerHA cluster communication and GRS replication traffic. Connectivity must support cross-location communication using IBM Cloud networking services such as Transit Gateway, VPN, or Direct Link. DNS resolution must support role changes during disaster recovery activation. |
| Security | Ensure encryption of data at rest and in transit for PowerVS storage and replication traffic. Protect management and application boundaries using IBM Cloud security controls. Administrative access to PowerHA and PowerVS resources must follow least-privilege principles. |
| Resiliency | Provide site-level disaster recovery for IBM i workloads using IBM PowerHA SystemMirror for i in combination with Global Replication Services (GRS) and IASP switching. This pattern is designed to target an Recovery Point Objective (RPO) of 15 minutes and an Recovery Time Objective (RTO) of 1 hour, subject to workload characteristics, replication behavior, network conditions, and operational procedures. This pattern does not provide local high availability. |
| Service Management | Enable monitoring and logging for PowerVS resources, PowerHA cluster events, storage replication status, and network connectivity using IBM Cloud observability services. Monitoring must support disaster recovery readiness, failover testing, and recovery validation. |
{: caption="Resiliency for PowerVS requirements" caption-side="bottom"}

RPO and RTO values are target objectives and are not guaranteed. Actual recovery times and data loss depend on replication lag at the time of failure, workload I/O characteristics, and operational readiness.
{: note}


## Components
{: #component-list}

| Category | Solution components | How it is used in a solution |
| - | - | - |
| Compute | PowerVS LPARs | - High availability workload virtual servers  \n - Disaster recovery workload virtual servers  \n - Global Replication Service (GRS) controllers |
| | VPC VSI | Compute for NGFW and management tools |
| Storage | Flash Storage from {{site.data.keyword.IBM_notm}} FS9500 series devices | Web, application, database storage, Storage for GRS |
| Networking | {{site.data.keyword.dl_full_notm}} | Enterprise to cloud network connectivity |
| | Transit Gateway (TGW) | Connectivity between PowerVS and VPCs                                                                                               |
| | Service Endpoints | Private network access to cloud services such {{site.data.keyword.logs_full_notm}}, Cloud Object Storage. |
| | [Global Transit Gateway (GTGW)](/docs/transit-gateway?topic=transit-gateway-about) | Provides PowerVS and VPC connectivity in different regions (global routing) |
| | [DNS Services](/docs/dns-svcs?topic=dns-svcs-about-dns-services) | Private DNS resolution |
| Security | Next-Generation Firewall (NGFW) | Provide IDS/IPS and edge firewall capabilities |
| Resiliency | PowerHA SystemMirror for i | Orchestration for Disaster Recovery between 2 GRS paired datacenters |
| | Global Replication Service | SAN to SAN replication between two {{site.data.keyword.cloud_notm}} data centers  |
| Service Management | {{site.data.keyword.logs_full_notm}} and {{site.data.keyword.monitoringlong_notm}} | Apps, Audit, and operational logs monitor platform metrics |
{: caption="Resiliency for PowerVS components" caption-side="bottom"}
