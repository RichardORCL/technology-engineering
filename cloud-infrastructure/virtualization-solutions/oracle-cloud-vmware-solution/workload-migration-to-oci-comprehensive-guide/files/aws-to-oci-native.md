# AWS to OCI Native Compute Instances

## Overview

This guide covers migrating workloads from Amazon Web Services (AWS) EC2 instances to OCI Native Compute Instances. Migration from AWS requires VM format conversion, networking adaptation, and integration with OCI services. This path suits organizations seeking to optimize costs, leverage OCI-native services, or consolidate cloud environments.

## Introduction

Oracle Cloud Infrastructure (OCI) is a global cloud services platform offering a comprehensive portfolio of IaaS, PaaS, SaaS, and DaaS capabilities across distributed datacenters. OCI Native Compute Instances are designed for replatformed or cloud-native workloads, offering secure, elastic, and high-performance virtual machines provisioned directly within OCI. Migrating from AWS to OCI enables organizations to leverage OCI's competitive pricing, high-performance infrastructure, and integrated cloud services.

## Target Platform: OCI Native Compute Instances

**Key Characteristics:**
- **Hypervisor:** OCI KVM
- **Management Tools:** OCI Console
- **Best Use Case:** Cloud-native apps, replatformed VMs, containers
- **Compute Shapes:** Flexibly defined OCPU/RAM; Standard, DenseIO, GPU, HPC, Ampere
- **Networking:** OCI Virtual Cloud Network (VCN)
- **Primary Storage:** OCI Block Volumes
- **Supported OS:** Latest Linux/Windows distributions

## Migration Tools

### Oracle Cloud Migrations (OCM) (Recommended)

Oracle Cloud Migrations is a managed service that automates the migration of workloads—specifically AWS EC2 instances—to Oracle Cloud Infrastructure (OCI). It streamlines every step of the process—from discovery and planning to replication and deployment—using OCI Console, CLI, or API interface.

**Key Capabilities:**

**Automated Asset Discovery & Inventory:**
- For AWS, the service performs agentless discovery of EC2 instances and EBS volumes.
- Discovered assets are stored in an OCI-hosted Inventory, along with performance data available in the OCI Monitoring service.

**Migration Planning & Execution:**
- Assets are grouped into Migration Projects, each containing one or more Migration Plans.
- The service recommends target OCI configurations—such as compute shape and placement—based on source attributes and performance metrics. Users can customize these plans and evaluate cost estimates.
- Supports incremental replication of instance data to OCI.

**Secure, Compliant Access & Governance:**
- Integrates with OCI Identity and Access Management (IAM) and Vault to ensure secure authentication, authorization, and management of credentials.
- Supports AWS IAM roles and credentials for secure access to source environments.
- Administrators must configure compartments, dynamic groups, and IAM policies to enable migration components and access control.

**Supported Environments:**
- AWS EC2 (x86/EBS-backed instances).
- Supports a wide range of Linux and Windows guest OS versions.
- Supports both standard and Nitro-based EC2 instances.

Oracle Cloud Migrations provides a streamlined, end-to-end migration experience for AWS workloads moving into OCI. From discovery through validation and cutover, the service offers automation, governance integration, cost insights, and incremental replication. It's ideal for organizations seeking a secure, self-service migration path that integrates directly with OCI infrastructure.

**Best Suited For:**
- AWS EC2 instances requiring automated discovery and migration planning
- Organizations seeking integration with OCI-native services
- Workloads that benefit from OCI's cost optimization and performance

### RackWare (Alternative)

RackWare is a cloud-agnostic workload mobility and resilience platform that simplifies migration, disaster recovery, and backup across physical, virtual, and cloud-native environments. Its core product, the RackWare Management Module (RMM), provides agentless, policy driven automation to move and protect workloads.

**Key Benefits:**
- **Broad Compatibility** – Supports AWS EC2, Azure VMs, GCP Compute Engine, VMware, Hyper-V, KVM, physical servers, and containers (Kubernetes). Works with all major public clouds, including OCI.
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

RMM is available in OCI Marketplace with built-in support for OCI Native migrations from AWS and other cloud providers.

**Best Suited For:**
- Complex migrations requiring policy-driven automation
- Organizations requiring migration, DR, and backup in a single platform
- Multi-cloud environments with workloads across AWS, Azure, and GCP

## Assessment and Discovery Mapping

A structured assessment, planning, and testing phase is essential for validating the migration design, minimizing risk, and ensuring a smooth transition to OCI Native Compute Instances. This phase should include:

- **Workload Discovery & Classification** – Use inventory tools (e.g., OCM discovery, AWS Systems Manager, CloudFormation, or RackWare) to map out EC2 instances, applications, and dependencies. Classify workloads by criticality (mission-critical, business-critical, dev/test) and migration complexity.
- **Dependency Mapping** – Identify application interdependencies, DNS records, firewall rules, and IP address requirements. Map AWS VPC networking, security groups, and route tables to OCI equivalents. Note that IP addresses will need to be remapped unless using custom networking solutions.
- **Right-Sizing & Capacity Planning** – Assess CPU, memory, and storage utilization to define the required OCI compute shape and storage architecture (OCI Block Volumes). Consider current utilization and growth factors. OCM provides automated recommendations based on AWS CloudWatch performance metrics.
- **Network & Security Planning** – Validate OCI networking design, including VCNs, subnets, security lists, and FastConnect/IPSec VPN. Map AWS security groups to OCI security lists and network security groups. Plan for IP remapping and DNS updates.
- **Cost Analysis** – Compare AWS costs with OCI pricing to identify cost optimization opportunities. OCM provides cost estimates during migration planning.
- **Testing & Validation** – Conduct pilot migrations for representative workloads before executing large-scale cutovers. Validate performance, failover, and recovery procedures.

## Migration Considerations

### Format Conversion

AWS EC2 instances use EBS volumes (backed by various formats), which must be converted to OCI-compatible formats during migration. Both OCM and RackWare handle this conversion automatically.

### Networking Changes

Migrating from AWS to OCI Native requires:

- **IP Address Remapping:** EC2 instances will receive new IP addresses in OCI VCNs
- **VPC to VCN Mapping:** AWS VPCs map to OCI VCNs; subnets map to OCI subnets
- **Security Groups:** AWS security groups map to OCI security lists and network security groups
- **Route Tables:** AWS route tables need to be recreated in OCI
- **DNS Updates:** DNS records must be updated to point to new IP addresses
- **Gateway Configuration:** Internet gateways, NAT gateways, and VPN connections need OCI equivalents
- **Elastic IPs:** AWS Elastic IPs cannot be migrated; plan for new public IP addresses in OCI

### Storage Migration

- **EBS Volumes:** AWS EBS volumes are converted to OCI Block Volumes
- **Instance Store:** Ephemeral instance store volumes are not migrated; ensure data is on EBS
- **EBS Snapshots:** Can be used as a migration source, but direct volume migration is preferred
- **Storage Performance:** Map AWS EBS volume types (gp3, io1, io2) to OCI storage performance tiers (Standard, Balanced, High Performance)
- **Disk Sizes:** Disk sizes and performance characteristics may need adjustment

### AWS-Specific Considerations

- **Instance Types:** AWS instance types map to OCI compute shapes; OCM provides automated recommendations
- **IAM Roles:** AWS IAM instance roles are not migrated; use OCI Instance Principals or IAM policies
- **CloudWatch:** AWS CloudWatch monitoring is replaced with OCI Monitoring service
- **Systems Manager:** AWS Systems Manager agents are replaced with OCI agents
- **Elastic Load Balancers:** AWS ELBs/ALBs need to be replaced with OCI Load Balancers
- **Auto Scaling Groups:** AWS Auto Scaling Groups need to be recreated using OCI Auto Scaling
- **S3 Integration:** Applications using S3 need to migrate to OCI Object Storage
- **RDS Databases:** AWS RDS instances require separate database migration strategies (not covered by VM migration tools)

### Operating System Considerations

- **Amazon Linux:** Amazon Linux instances may need OS updates or conversion to standard Linux distributions
- **Windows AMIs:** Windows instances may need driver updates for OCI compatibility
- **SSM Agent:** AWS Systems Manager agent will be removed; OCI agents may need to be installed
- **Cloud-Init:** AWS user-data scripts may need adaptation for OCI cloud-init
- **Instance Metadata:** AWS instance metadata service is replaced with OCI instance metadata service

### Cost Optimization Opportunities

- **Reserved Instances:** AWS Reserved Instances cannot be transferred; evaluate OCI Universal Credits or Reserved Capacity
- **Spot Instances:** AWS Spot Instances are replaced with OCI Preemptible Instances for cost savings
- **Storage Costs:** OCI Block Storage often provides better price-performance than AWS EBS
- **Data Transfer:** Plan for data transfer costs during migration; consider using FastConnect for large migrations

## Special Considerations for Enterprise and Mission Critical Databases

While VM-level migration tools (e.g., OCM, RackWare) can handle the majority of workloads, they may not be sufficient for enterprise-scale, mission-critical applications where downtime is unacceptable. For large and transaction-heavy databases, a pure VM-level migration introduces significant challenges due to:
- Database size (terabytes or petabytes).
- Continuous write activity (transaction-heavy workloads).
- The requirement for zero or near-zero downtime.

To migrate such mission critical workloads and DB's you might consider dedicated solutions and architectures:

- **AWS RDS to OCI Databases:** 
  - **Oracle Databases** – Use Oracle Data Guard or GoldenGate for robust replication, synchronization, and failover capabilities.
  - **Microsoft SQL Server** – Implement Always On Availability Groups to ensure transactional consistency and minimize downtime.
  - **PostgreSQL/MySQL** – Use native replication or database-specific migration tools.
- **Self-Managed Databases on EC2:** 
  - **Oracle Databases** – Use Oracle Data Guard or GoldenGate for robust replication, synchronization, and failover capabilities.
  - **Microsoft SQL Server** – Implement Always On Availability Groups to ensure transactional consistency and minimize downtime.
  - **Microsoft Active Directory** – Use native AD replication between domain controllers to maintain consistency.
  - **Microsoft Exchange Server** – Leverage Exchange Hybrid configurations or Database Availability Groups (DAGs) for continuity during migration.

By combining VM-level mobility with application-aware replication, enterprises can achieve data consistency, reduced downtime, and a resilient cutover strategy for their most business-critical workloads.

## Best Practices & Guidance

To ensure a smooth and resilient transition to OCI Native Compute Instances, the following best practices should be incorporated into any migration strategy:

- **Adopt a phased migration approach** – Start with lower-priority or non-production workloads to validate tooling, processes, and network designs. Use early phases as learning cycles before addressing mission-critical systems.

- **Plan for IP remapping** – Document all IP addresses, DNS records, and firewall rules that will need updating. Create a comprehensive IP mapping table and security group mapping before migration begins.

- **Map AWS services to OCI equivalents** – Create a service mapping document covering ELB to OCI Load Balancer, CloudWatch to OCI Monitoring, S3 to Object Storage, etc.

- **Validate bandwidth and connectivity** – Confirm that FastConnect or VPN capacity can handle large-scale data transfers without impacting production traffic. Monitor latency, throughput, and error rates throughout the migration.

- **Establish rollback procedures** – Define clear fallback and recovery scenarios in case of migration failure. Keep source AWS resources available during the migration window. Document rollback steps and ensure that teams are trained to execute them under time pressure.

- **Implement robust testing** – Conduct end-to-end validation of application behavior, security controls, and performance benchmarks in the target environment before go-live. Include functional, load, and failover testing.

- **Engage stakeholders early** – Involve application owners, database administrators, network and security teams during the planning stage. Align technical decisions with business objectives, compliance requirements, and service-level agreements (SLAs).

- **Maintain comprehensive documentation** – Capture migration plans, cutover runbooks, rollback steps, and lessons learned to ensure repeatability and knowledge transfer across teams.

- **Use automation where possible** – Leverage orchestration and migration tooling (e.g., OCM migration plans, RackWare workflows) to reduce manual effort and minimize human error.

- **Plan for post-migration optimization** – After workloads are stable in OCI, review performance, right-size compute shapes, and integrate with OCI's managed services (e.g., monitoring, security, backup, databases) to maximize efficiency and cost savings.

- **Prioritize security and compliance** – Ensure IAM policies, network security lists, encryption settings, and audit configurations are validated before workloads are exposed to production use. Map AWS security best practices to OCI equivalents.

- **Leverage OCI-native services** – Take advantage of OCI's managed services such as Autonomous Database, Object Storage, and monitoring services to modernize applications post-migration.

- **Optimize costs** – Review OCI pricing models, consider Reserved Capacity or Universal Credits, and right-size instances based on actual utilization patterns.
