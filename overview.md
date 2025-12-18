---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

This pattern describes a high-availability and disaster recovery (HA/DR) architecture using IBM PowerHA SystemMirror with Global Replication Services (GRS) deployed on IBM Power Virtual Server (PowerVS). The solution is designed for enterprise workloads running on IBM i that require resilient operations across geographically separated PowerVS locations.

The pattern focuses on providing application continuity within a PowerVS location using PowerHA, combined with asynchronous replication and disaster recovery across PowerVS locations using GRS. Together, these technologies enable automated failover for local outages and coordinated recovery for regional-level failures.

This deployment guide provides architectural guidance, configuration considerations, and operational best practices for implementing PowerHA and GRS in PowerVS environments.

## Architecture Overview
{: #arch-overview}

The solution is built around two primary PowerVS locations, each hosting a Power Virtual Server instance and associated IBM Flashsystem storage volumes.

- PowerHA SystemMirror is used to manage cluster membership, resource groups, and failover within the solution.
- Global Replication Services (GRS) provides asynchronous replication of storage volumes between PowerVS locations.
- PowerVS logical partitions (LPARs) host the application workloads and participate in the PowerHA cluster.
- IBM Cloud networking connects PowerVS instances and enables replication traffic and cluster communication.

## Solution Capabilities
{: #solution-capabilities}

This pattern enables the following capabilities:
- High availability within a PowerVS environment using PowerHA clustering
- Disaster recovery across PowerVS locations using GRS asynchronous replication
- Automated resource group management and application restart
- Consistent recovery point objectives (RPO) based on GRS replication behavior
- Planned and unplanned failover support
- Support for enterprise workloads requiring cross-location resiliency

## Pattern considerations
{: #considerations}

This pattern assumes:
- PowerVS instances are provisioned in supported regions for GRS
- IBM Flashsystem Storage volumes are used for application data
- PowerHA SystemMirror version compatible with PowerVS is installed and licensed
- GRS is configured between the selected PowerVS locations
- Network connectivity and security requirements are in place prior to deployment

Detailed installation, configuration, and validation steps are covered in subsequent sections of this deployment guide.

The following topics are not covered in this pattern:
- Application-specific clustering logic
- Multi-site active-active configurations
- Backup and long-term retention strategies
- Performance tuning beyond baseline PowerVS recommendations
- Non-PowerVS environments


You can validate that offerings are available in the regions you are deploying by using the [{{site.data.keyword.cloud_notm}} portal](https://cloud.ibm.com/login){: external}.
{: note}
