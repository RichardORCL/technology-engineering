# Other Public Clouds to OCI Native Compute Instances

## Overview

This guide covers migrating workloads from other public cloud providers (Microsoft Azure, Google Cloud Platform (GCP), and other cloud environments) to OCI Native Compute Instances. Migration from these platforms requires VM format conversion, networking adaptation, and integration with OCI services. This path suits organizations seeking to optimize costs, leverage OCI-native services, consolidate cloud environments, or avoid vendor lock-in.

## Introduction

Oracle Cloud Infrastructure (OCI) is a global cloud services platform offering a comprehensive portfolio of IaaS, PaaS, SaaS, and DaaS capabilities across distributed datacenters. OCI Native Compute Instances are designed for replatformed or cloud-native workloads, offering secure, elastic, and high-performance virtual machines provisioned directly within OCI. Migrating from other public clouds to OCI enables organizations to leverage OCI's competitive pricing, high-performance infrastructure, integrated cloud services, and avoid dependency on a single cloud provider.

## Target Platform: OCI Native Compute Instances

**Key Characteristics:**
- **Hypervisor:** OCI KVM
- **Management Tools:** OCI Console
- **Best Use Case:** Cloud-native apps, replatformed VMs, containers
- **Compute Shapes:** Flexibly defined OCPU/RAM; Standard, DenseIO, GPU, HPC, Ampere
- **Networking:** OCI Virtual Cloud Network (VCN)
- **Primary Storage:** OCI Block Volumes
- **Supported OS:** Latest Linux/Windows distributions

## Migration Tool: RackWare (Primary)

RackWare is a cloud-agnostic workload mobility and resilience platform that simplifies migration, disaster recovery, and backup across physical, virtual, and cloud-native environments. Its core product, the RackWare Management Module (RMM), provides agentless, policy driven automation to move and protect workloads.

**Key Benefits:**
- **Broad Compatibility** – Supports Azure VMs, GCP Compute Engine, AWS EC2, VMware, Hyper-V, KVM, physical servers, and containers (Kubernetes). Works with all major public clouds, including OCI.
- **Agentless & Lightweight** – No permanent agents required; minimal impact on production systems.
- **Multi-Use Platform** – One solution for migration, DR, and backup—reducing tool sprawl and complexity.
- **Efficient Replication** – Delta-based sync ensures faster migrations and near-zero RPO/RTO for DR.
- **Scalability** – Handles migrations from a few to thousands of workloads in coordinated "waves."
- **Oracle Integration** – Available on Oracle Cloud Marketplace; supports OCI and on-prem Oracle environments (C3, PCA).

**Key Capabilities:**
- **Migration** – workload migrations with automated sizing and IP/DNS remapping.
- **Disaster Recovery** – Policy-based DR with automated failover/fallback and non-disruptive testing.
- **Backup** – Application-consistent snapshots, long-term retention, and granular restore.
- **Heterogeneous Support** – Windows, Linux, and container workloads.
- **Automated Discovery** – Discovers VMs, dependencies, and performance metrics across cloud platforms.

RMM is available in OCI Marketplace with built-in support for OCI Native migrations from Azure, GCP, and other cloud providers.

**Best Suited For:**
- Multi-cloud environments with workloads across Azure, GCP, AWS, and other providers
- Organizations requiring migration, DR, and backup in a single platform
- Complex migrations requiring policy-driven automation and dependency mapping
- Cloud-to-cloud migrations where native tools are not available

## Assessment and Discovery Mapping

A structured assessment, planning, and testing phase is essential for validating the migration design, minimizing risk, and ensuring a smooth transition to OCI Native Compute Instances. This phase should include:

- **Workload Discovery & Classification** – Use inventory tools (e.g., RackWare discovery, cloud-native inventory tools, Azure Resource Manager, GCP Cloud Asset Inventory) to map out virtual machines, applications, and dependencies. Classify workloads by criticality (mission-critical, business-critical, dev/test) and migration complexity.
- **Dependency Mapping** – Identify application interdependencies, DNS records, firewall rules, and IP address requirements. Map cloud-specific networking (Azure VNets, GCP VPCs) to OCI VCNs. Note that IP addresses will need to be remapped unless using custom networking solutions.
- **Right-Sizing & Capacity Planning** – Assess CPU, memory, and storage utilization to define the required OCI compute shape and storage architecture (OCI Block Volumes). Consider current utilization and growth factors. RackWare provides automated sizing recommendations based on performance metrics.
- **Network & Security Planning** – Validate OCI networking design, including VCNs, subnets, security lists, and FastConnect/IPSec VPN. Map cloud-specific security (Azure NSGs, GCP firewall rules) to OCI security lists and network security groups. Plan for IP remapping and DNS updates.
- **Cost Analysis** – Compare cloud provider costs with OCI pricing to identify cost optimization opportunities.
- **Testing & Validation** – Conduct pilot migrations for representative workloads before executing large-scale cutovers. Validate performance, failover, and recovery procedures.

## Migration Considerations

### Format Conversion

**Azure:** Azure VMs use VHD/VHDX disk formats (managed disks), which must be converted to OCI-compatible formats during migration. RackWare handles this conversion automatically.

**GCP:** GCP Compute Engine instances use persistent disks (backed by various formats), which must be converted to OCI-compatible formats. RackWare supports these conversions.

**Other Clouds:** Various disk formats are supported; RackWare handles format conversion automatically.

### Networking Changes

Migrating from other public clouds to OCI Native requires:

- **IP Address Remapping:** VMs will receive new IP addresses in OCI VCNs
- **Virtual Network Mapping:** 
  - Azure VNets map to OCI VCNs
  - GCP VPCs map to OCI VCNs
  - Subnets map to OCI subnets
- **Security Groups:** 
  - Azure Network Security Groups (NSGs) map to OCI security lists and network security groups
  - GCP firewall rules map to OCI security lists
- **Route Tables:** Cloud-specific route tables need to be recreated in OCI
- **DNS Updates:** DNS records must be updated to point to new IP addresses
- **Gateway Configuration:** Internet gateways, NAT gateways, and VPN connections need OCI equivalents
- **Load Balancers:** Cloud-specific load balancers need to be replaced with OCI Load Balancers

### Storage Migration

**Azure:**
- Managed Disks (Premium SSD, Standard SSD, Standard HDD) are converted to OCI Block Volumes
- Unmanaged disks require additional handling
- Azure Blob Storage needs separate migration to OCI Object Storage

**GCP:**
- Persistent Disks (SSD, Standard) are converted to OCI Block Volumes
- GCP Cloud Storage needs separate migration to OCI Object Storage

**Storage Performance:** Map cloud-specific storage tiers to OCI storage performance tiers (Standard, Balanced, High Performance)

### Cloud-Specific Considerations

#### Microsoft Azure

- **Instance Types:** Azure VM sizes map to OCI compute shapes; RackWare provides automated recommendations
- **Managed Identities:** Azure Managed Identities are not migrated; use OCI Instance Principals or IAM policies
- **Azure Monitor:** Azure Monitor is replaced with OCI Monitoring service
- **Azure Automation:** Azure Automation runbooks need to be adapted for OCI
- **Azure Load Balancer:** Azure Load Balancers need to be replaced with OCI Load Balancers
- **Virtual Machine Scale Sets:** Azure VMSS need to be recreated using OCI Auto Scaling
- **Azure Key Vault:** Azure Key Vault secrets need to be migrated to OCI Vault
- **Azure Backup:** Azure Backup policies need to be recreated in OCI

#### Google Cloud Platform (GCP)

- **Instance Types:** GCP machine types map to OCI compute shapes; RackWare provides automated recommendations
- **Service Accounts:** GCP service accounts are not migrated; use OCI Instance Principals or IAM policies
- **Cloud Monitoring:** GCP Cloud Monitoring is replaced with OCI Monitoring service
- **Cloud Logging:** GCP Cloud Logging is replaced with OCI Logging service
- **Cloud Load Balancing:** GCP load balancers need to be replaced with OCI Load Balancers
- **Instance Groups:** GCP managed/unmanaged instance groups need to be recreated using OCI Auto Scaling
- **Cloud Storage:** GCP Cloud Storage buckets need separate migration to OCI Object Storage
- **Cloud SQL:** GCP Cloud SQL instances require separate database migration strategies

#### Other Cloud Providers

- **Alibaba Cloud:** Similar considerations as Azure/GCP; RackWare supports Alibaba Cloud ECS instances
- **IBM Cloud:** Similar considerations; RackWare supports IBM Cloud VPC instances
- **Other Providers:** RackWare's broad compatibility supports most major cloud providers

### Operating System Considerations

- **Cloud-Specific Images:** Cloud provider-specific OS images may need updates or conversion to standard distributions
- **Windows Instances:** Windows instances may need driver updates for OCI compatibility
- **Cloud Agents:** Cloud-specific agents (Azure VM Agent, GCP guest environment) will be removed; OCI agents may need to be installed
- **Cloud-Init:** Cloud-specific user-data scripts may need adaptation for OCI cloud-init
- **Instance Metadata:** Cloud-specific instance metadata services are replaced with OCI instance metadata service

### Cost Optimization Opportunities

- **Reserved Instances/Capacity:** Cloud provider reserved capacity cannot be transferred; evaluate OCI Universal Credits or Reserved Capacity
- **Spot/Preemptible Instances:** Cloud provider spot/preemptible instances are replaced with OCI Preemptible Instances for cost savings
- **Storage Costs:** OCI Block Storage often provides better price-performance than other cloud providers
- **Data Transfer:** Plan for data transfer costs during migration; consider using FastConnect for large migrations
- **Multi-Cloud Discounts:** Consolidating to OCI may provide volume discounts

## Special Considerations for Enterprise and Mission Critical Databases

While VM-level migration tools like RackWare can handle the majority of workloads, they may not be sufficient for enterprise-scale, mission-critical applications where downtime is unacceptable. For large and transaction-heavy databases, a pure VM-level migration introduces significant challenges due to:
- Database size (terabytes or petabytes).
- Continuous write activity (transaction-heavy workloads).
- The requirement for zero or near-zero downtime.

To migrate such mission critical workloads and DB's you might consider dedicated solutions and architectures:

- **Managed Database Services:**
  - **Azure SQL Database / SQL Managed Instance** – Use native replication or database migration tools to migrate to OCI Autonomous Database or self-managed databases.
  - **GCP Cloud SQL** – Use native replication or database migration tools to migrate to OCI Autonomous Database or self-managed databases.
  - **Oracle Databases** – Use Oracle Data Guard or GoldenGate for robust replication, synchronization, and failover capabilities.
  - **Microsoft SQL Server** – Implement Always On Availability Groups to ensure transactional consistency and minimize downtime.
- **Self-Managed Databases on VMs:**
  - **Oracle Databases** – Use Oracle Data Guard or GoldenGate for robust replication, synchronization, and failover capabilities.
  - **Microsoft SQL Server** – Implement Always On Availability Groups to ensure transactional consistency and minimize downtime.
  - **Microsoft Active Directory** – Use native AD replication between domain controllers to maintain consistency.
  - **Microsoft Exchange Server** – Leverage Exchange Hybrid configurations or Database Availability Groups (DAGs) for continuity during migration.

By combining VM-level mobility with application-aware replication, enterprises can achieve data consistency, reduced downtime, and a resilient cutover strategy for their most business-critical workloads.

## Best Practices & Guidance

To ensure a smooth and resilient transition to OCI Native Compute Instances, the following best practices should be incorporated into any migration strategy:

- **Adopt a phased migration approach** – Start with lower-priority or non-production workloads to validate tooling, processes, and network designs. Use early phases as learning cycles before addressing mission-critical systems.

- **Plan for IP remapping** – Document all IP addresses, DNS records, and firewall rules that will need updating. Create a comprehensive IP mapping table and security group mapping before migration begins.

- **Map cloud services to OCI equivalents** – Create a service mapping document covering load balancers, monitoring, storage, databases, and other managed services. Identify which services can be migrated and which require reimplementation.

- **Validate bandwidth and connectivity** – Confirm that FastConnect or VPN capacity can handle large-scale data transfers without impacting production traffic. Monitor latency, throughput, and error rates throughout the migration.

- **Establish rollback procedures** – Define clear fallback and recovery scenarios in case of migration failure. Keep source cloud resources available during the migration window. Document rollback steps and ensure that teams are trained to execute them under time pressure.

- **Implement robust testing** – Conduct end-to-end validation of application behavior, security controls, and performance benchmarks in the target environment before go-live. Include functional, load, and failover testing.

- **Engage stakeholders early** – Involve application owners, database administrators, network and security teams during the planning stage. Align technical decisions with business objectives, compliance requirements, and service-level agreements (SLAs).

- **Maintain comprehensive documentation** – Capture migration plans, cutover runbooks, rollback steps, and lessons learned to ensure repeatability and knowledge transfer across teams.

- **Use automation where possible** – Leverage orchestration and migration tooling (e.g., RackWare workflows) to reduce manual effort and minimize human error.

- **Plan for post-migration optimization** – After workloads are stable in OCI, review performance, right-size compute shapes, and integrate with OCI's managed services (e.g., monitoring, security, backup, databases) to maximize efficiency and cost savings.

- **Prioritize security and compliance** – Ensure IAM policies, network security lists, encryption settings, and audit configurations are validated before workloads are exposed to production use. Map cloud-specific security best practices to OCI equivalents.

- **Leverage OCI-native services** – Take advantage of OCI's managed services such as Autonomous Database, Object Storage, and monitoring services to modernize applications post-migration.

- **Optimize costs** – Review OCI pricing models, consider Reserved Capacity or Universal Credits, and right-size instances based on actual utilization patterns. Compare total cost of ownership across cloud providers.

- **Plan for multi-cloud scenarios** – If maintaining workloads in multiple clouds, establish consistent operational procedures and consider using RackWare for ongoing DR and backup across clouds.
