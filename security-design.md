---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Security design
{: #security-design}

The following are requirements for the security aspect for the PowerHA and Global Replication Services on PowerVS:

- Help ensure data encryption at rest and in transit for the storage layer.

- Protect the boundaries of the application against denial-of-service and application-layer attacks.

## Security design considerations
{: #security-design-IBMi}

Security should be applied at all layers of the solution using a defense in depth approach based on customer requirements. This includes security controls at the network edge, within virtual private clouds, and on IBM i logical partitions, as well as at the application and data layers. Data should be protected both in transit and at rest according to data classification requirements. Controls should be in place to minimize the need for direct administrative access to the environment, and when such access is required, all access should be logged and monitored.

It is a best practice to isolate, secure, manage, and monitor ingress and egress traffic to the environment and to centralize these functions in a hub and spoke model using an Edge or Transit VPC. Security and isolation can be established by using a combination of Security Groups, Network Access Control Lists, and routing and firewall rules defined in the Edge or Transit VPC. This is in addition to the cloud account level protection provided by IBM Cloud Identity and Access Management services.

Storage replication between Power Virtual Server locations is performed by Global Replication Services and does not traverse customer managed networks or require customer managed firewall rules, network access control lists, or encryption configuration.

IBM PowerHA SystemMirror for i operates entirely within the IBM i operating system and does not introduce additional externally exposed management endpoints or network services. Application and operating system level security controls remain unchanged during disaster recovery operations and are preserved as part of the Independent Auxiliary Storage Pool.

Consider the following:
- Firewalls deployed in the Edge or Transit VPC can be used to secure and route traffic between the IBM Cloud environment and customer networks.
- Logging should be enabled to support firewall activity analysis and to meet customer security monitoring requirements.
- Public and private traffic should be routed through the Edge or Transit VPC to support centralized routing, isolation, and logging.
- A bastion host is recommended in the Edge or Transit VPC to centralize and audit administrative access to the environment where direct access to IBM i logical partitions is restricted. Bastion access and activity should be logged and monitored using Privileged Access Management services.
