---

copyright:
  years: 2025
lastupdated: "2025-12-17"

subcollection: pattern-pvs-ibmi-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Network design
{: #network-design}

Network design is a pivotal element in crafting a resilient architecture. It outlines the interconnections and communication pathways among various solution components. This process entails choosing optimal network connections for replication, while also considering the effects of bandwidth and distance on network latency. In this section, the essential networking considerations for enhancing resilience through effective network design are discussed.

This resiliency pattern uses a two-region deployment for disaster recovery. The requirements for the network aspect for the resiliency on {{site.data.keyword.powerSysShort}} workloads pattern focus on:

- Resilience for enterprise connectivity to the {{site.data.keyword.powerSysShort}} disaster recovery environment
- Connectivity between data centers

## Network design considerations
{: #network-design-consideratons}

IBM Power Virtual Server integrates with IBM Cloud networking services to provide secure, private connectivity for IBM i workloads. PowerVS logical partitions (LPARs) use customer-managed networking for system access, administration, and inter-system communication.

In this pattern, networking is required to support communication between primary and recovery PowerVS locations for disaster recovery coordination and management.

### Network latency
{: #network-latency}

Network latency refers to the amount of time that it takes for data to travel from one point to another across a network. In our pattern, latency effects two aspects: high availability and disaster recovery. 

Review the latency considerations based on this pattern:

- This pattern is using Global Replication Services (GRS) which operates between two sites that are over 300 km apart. 
- Greater distances typically need asynchronous replication. This is because asynchronous replication is designed to work over longer distances. 
- Depending on the application, synchronous mirroring might be the only practical approach.

### Network connectivity for disaster recovery
{: #network-connectivity}

Network connectivity between PowerVS locations is required to support PowerHA SystemMirror for i cluster communication and disaster recovery orchestration. PowerHA uses network connectivity for control-plane operations such as role coordination, recovery initiation, and system management.

Global Replication Services (GRS) does not rely on customer-managed network connectivity between IBM i LPARs. Storage replication traffic is handled entirely within the PowerVS service infrastructure and is transparent to the IBM i operating system. No application data replication flows over customer-managed networks.

### Connectivity options
{: #network-connectivity-options}

IBM Cloud provides multiple options to establish secure connectivity between PowerVS locations and enterprise environments, including IBM Cloud Transit Gateway, site-to-site VPN, and IBM Cloud Direct Link. These options can be used to enable administrative access and disaster recovery coordination between sites.

The selected connectivity option must support reliable communication for PowerHA orchestration and operational access but does not need to be sized or optimized for storage replication traffic.

### Replication traffic
{: #replication-traffic}

Replication traffic refers to the data transmitted between systems during the process of replicating or copying information from one location to another. 

Review the key considerations for replication traffic when implementing backup and disaster recovery:

- Global replication traffic between {{site.data.keyword.powerSysShort}} regions traverses the {{site.data.keyword.cloud_notm}} backbone.
- GRS control Logical Partition (LPAR) traffic traverses the Global Transit Gateway (GTGW).

## Virtual Private Cloud (VPC)
{: #virtual-private-cloud}

Multiple VPCs are used in this pattern. Additional client requirements might require additional VPCs. This pattern includes:

- Management VPC: Next Generation Firewall (NGFW) is deployed in the Management VPC. To provide isolation and centralized advanced security functions, the network design follows the hub and spoke VPC model. The management VPC serves as the hub for which all ingress and egress traffic flows. The management VPC is a virtual network VPC that acts as a central point of connectivity to on-premises network and all other VPCs. {{site.data.keyword.powerSysShort}} workspaces are connected to the management VPC also know as the hub by a {{site.data.keyword.tg_short}}, which allows traffic routing between the VPCs and {{site.data.keyword.powerSysShort}} workspaces in the {{site.data.keyword.cloud_notm}} account.

### High availability clusters
{: #highavailability-clusters}

Each {{site.data.keyword.powerSysShort}} needs its own IP which is a service IP typically on the same VLAN as the partner Power VSI. The service IP is an extra IP used for the application that is impacted by HA.

### DNS and name resolution
{: #dns-name}

Domain Name System (DNS) services must be designed to support disaster recovery operations. Applications, users, and administrators must be able to access IBM i workloads at the recovery site following a disaster recovery event.

DNS design should support role changes between primary and recovery sites and allow timely updates during failover and failback operations.

### Network security considerations
{: #security-considerations}

Network traffic used for PowerHA orchestration and administrative access must be protected to ensure confidentiality and integrity. Security considerations include encryption of data in transit, network segmentation to isolate management traffic, and controlled access to IBM i and PowerHA administrative interfaces.

Security controls should align with enterprise security policies and IBM Cloud best practices.
