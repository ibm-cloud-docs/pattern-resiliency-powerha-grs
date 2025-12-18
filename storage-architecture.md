---

copyright:
  years: 2025
lastupdated: "2025-12-18"

subcollection: pattern-resiliency-powerha-grs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #storage-decisions}

| Architecture decision | Requirement | Alternatives | Decision | Rationale |
|---|---|---|---|---|
| Primary storage for IBM i workloads | Provide highly available storage for IBM i application data | PowerVS managed storage tiers | PowerVS managed storage tiers | IBM i application data is stored in an Independent Auxiliary Storage Pool hosted on IBM Cloud managed PowerVS storage. |
| Independent Auxiliary Storage Pool (IASP) usage | Isolate application data to support disaster recovery | System ASP | Independent Auxiliary Storage Pool | IASP is required to enable controlled activation of application data at the recovery site and is mandatory for PowerHA SystemMirror for i with Global Replication Services. |
| Cross-location storage replication | Replicate application data between Power Virtual Server locations | Application-level replication | Global Replication Services | Global Replication Services provides asynchronous storage replication at the PowerVS storage layer and operates independently of the IBM i operating system. |
| Storage tier consistency | Ensure compatible performance characteristics across sites | Mixed storage tiers | Match storage tiers between sites | Global Replication Services requires like-for-like storage tiers between primary and recovery locations to support consistent replication behavior. |
| Recovery site storage provisioning | Provide storage to support disaster recovery activation | Reduced-capacity storage | Match production storage capacity | Recovery site storage must be provisioned to support full activation of the replicated Independent Auxiliary Storage Pool during a disaster recovery event. |
{: caption="Storage architecture decisions" caption-side="bottom"}
