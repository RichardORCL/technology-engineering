# Hyper-V/KVM to OCI Native Compute Instances

## Overview

This guide covers migrating workloads from Microsoft Hyper-V or KVM environments to OCI Native Compute Instances. Migration from Hyper-V or KVM requires VM format conversion and deployment into OCI's compute environment. This path suits organizations aiming to modernize workloads or consolidate platforms.

## Introduction

Oracle Cloud Infrastructure (OCI) is a global cloud services platform offering a comprehensive portfolio of IaaS, PaaS, SaaS, and DaaS capabilities across distributed datacenters. OCI Native Compute Instances are designed for replatformed or cloud-native workloads, offering secure, elastic, and high-performance virtual machines provisioned directly within OCI. This option is ideal for modernizing applications, integrating with OCI-native services, or optimizing costs and scalability.

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

### RackWare (Recommended)

RackWare is a cloud-agnostic workload mobility and resilience platform that simplifies migration, disaster recovery, and backup across physical, virtual, and cloud-native environments. Its core product, the RackWare Management Module (RMM), provides agentless, policy driven automation to move and protect workloads.

**Key Benefits:**
- **Broad Compatibility** – Supports VMware, Hyper-V, KVM, physical servers, and containers (Kubernetes). Works with all major public clouds, including OCI, AWS, Azure, and GCP.
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

RMM is available in OCI Marketplace with built-in support for OCI Native migrations.

**Best Suited For:**
- Hyper-V and KVM environments requiring migration to OCI Native
- Organizations requiring migration, DR, and backup in a single platform
- Complex migrations requiring policy-driven automation and dependency mapping

### VMware HCX Enterprise with OSAM (Alternative)

**OS-Assisted Migration (OSAM)** enables the migration of workloads from non-vSphere hypervisors, such as Microsoft Hyper-V or KVM, into OCI. Unlike standard vMotion or Bulk Migration, OSAM performs a guest-level migration by installing an HCX migration agent within the operating system. The agent copies the VM's disk and configuration data to the target environment, where the VM is then reconstructed.

**Note:** While OSAM is primarily designed for migration to Oracle Cloud VMware Solution (OCVS), it can be used as part of a multi-step migration process. For direct migration to OCI Native, RackWare is typically the preferred solution.

**Key Considerations:**
- OSAM requires an HCX Enterprise license
- Replication begins a full synchronization transfer to the destination site. The guest virtual machine remains online during replication until the final delta synchronization.
- After the full sync, the switch over can be immediate or at a specific schedule just like Bulk Migration
- Final delta sync starts when the switch phase starts, until then it maintains a continuous sync of changes
- HCX performs a hardware mapping of the replicated volumes to ensure proper operation, including updates of the software stack on the replica. This fix-up process includes adding drivers and modifying the OS configuration files at the destination. The migrated virtual machine reboots during this process.
- VMware Tools is installed on the migrated virtual machine and migration completes.
- OSAM does not support P2V

## Assessment and Discovery Mapping

A structured assessment, planning, and testing phase is essential for validating the migration design, minimizing risk, and ensuring a smooth transition to OCI Native Compute Instances. This phase should include:

- **Workload Discovery & Classification** – Use inventory tools (e.g., RackWare, Hyper-V Manager, KVM management tools) to map out virtual machines, applications, and dependencies. Classify workloads by criticality (mission-critical, business-critical, dev/test) and migration complexity.
- **Dependency Mapping** – Identify application interdependencies, DNS records, firewall rules, and IP address requirements. Tools such as RackWare support automated discovery, dependency mapping, and migration wave planning.
- **Right-Sizing & Capacity Planning** – Assess CPU, memory, and storage utilization to define the required OCI compute shape and storage architecture (OCI Block Volumes). Consider current utilization and growth factors.
- **Network & Security Planning** – Validate OCI networking design, including VCNs, subnets, security lists, and FastConnect/IPSec VPN. Plan for IP remapping and DNS updates.
- **Testing & Validation** – Conduct pilot migrations for representative workloads before executing large-scale cutovers. Validate performance, failover, and recovery procedures.

## Migration Considerations

### Format Conversion

**Hyper-V:** VHD/VHDX disk formats must be converted to OCI-compatible formats during migration. RackWare handles this conversion automatically.

**KVM:** QCOW2, RAW, or other KVM disk formats must be converted to OCI-compatible formats. RackWare supports these conversions.

### Networking Changes

Migrating to OCI Native requires:
- **IP Address Remapping:** VMs will receive new IP addresses in OCI VCNs
- **DNS Updates:** DNS records must be updated to point to new IP addresses
- **Firewall Rules:** Security lists and network security groups must be configured in OCI
- **Gateway Configuration:** Default gateways will change to OCI VCN gateways

### Storage Migration

- Hyper-V storage (VHD/VHDX) or KVM storage (QCOW2, RAW) is converted to OCI Block Volumes
- Disk sizes and performance characteristics may need adjustment
- Consider OCI storage performance tiers (Standard, Balanced, High Performance)

### Operating System Considerations

- Ensure guest OS versions are supported by OCI
- Hyper-V Integration Services or KVM-specific tools will be removed
- OCI-specific agents may need to be installed
- Some hypervisor-specific configurations may need adjustment

### Hyper-V Specific Considerations

- **Generation 1 vs Generation 2 VMs:** Both are supported, but Generation 2 VMs (UEFI) may have better compatibility
- **Dynamic Memory:** Convert to fixed memory allocation before migration
- **Checkpoints:** Snapshots/checkpoints are not migrated
- **SCSI vs IDE:** Ensure proper disk controller configuration

### KVM Specific Considerations

- **QCOW2 Snapshots:** Not migrated; consolidate before migration
- **Virtio Drivers:** May need adjustment for OCI-compatible drivers
- **CPU Models:** CPU features may need adjustment for OCI compatibility

## Special Considerations for Enterprise and Mission Critical Databases

While VM-level migration tools (e.g., RackWare) can handle the majority of workloads, they may not be sufficient for enterprise-scale, mission-critical applications where downtime is unacceptable. For large and transaction-heavy databases, a pure VM-level migration introduces significant challenges due to:
- Database size (terabytes or petabytes).
- Continuous write activity (transaction-heavy workloads).
- The requirement for zero or near-zero downtime.

To migrate such mission critical workloads and DB's you might consider dedicated solutions and architectures:
- **Oracle Databases** – Use Oracle Data Guard or GoldenGate for robust replication, synchronization, and failover capabilities.
- **Microsoft SQL Server** – Implement Always On Availability Groups to ensure transactional consistency and minimize downtime.
- **Microsoft Active Directory** – Use native AD replication between domain controllers to maintain consistency.
- **Microsoft Exchange Server** – Leverage Exchange Hybrid configurations or Database Availability Groups (DAGs) for continuity during migration.

By combining VM-level mobility with application-aware replication, enterprises can achieve data consistency, reduced downtime, and a resilient cutover strategy for their most business-critical workloads.

## Best Practices & Guidance

To ensure a smooth and resilient transition to OCI Native Compute Instances, the following best practices should be incorporated into any migration strategy:

- **Adopt a phased migration approach** – Start with lower-priority or non-production workloads to validate tooling, processes, and network designs. Use early phases as learning cycles before addressing mission-critical systems.

- **Plan for IP remapping** – Document all IP addresses, DNS records, and firewall rules that will need updating. Create a comprehensive IP mapping table before migration begins.

- **Validate bandwidth and connectivity** – Confirm that FastConnect or VPN capacity can handle large-scale data transfers without impacting production traffic. Monitor latency, throughput, and error rates throughout the migration.

- **Establish rollback procedures** – Define clear fallback and recovery scenarios in case of migration failure. Document rollback steps and ensure that teams are trained to execute them under time pressure.

- **Implement robust testing** – Conduct end-to-end validation of application behavior, security controls, and performance benchmarks in the target environment before go-live. Include functional, load, and failover testing.

- **Engage stakeholders early** – Involve application owners, database administrators, network and security teams during the planning stage. Align technical decisions with business objectives, compliance requirements, and service-level agreements (SLAs).

- **Maintain comprehensive documentation** – Capture migration plans, cutover runbooks, rollback steps, and lessons learned to ensure repeatability and knowledge transfer across teams.

- **Use automation where possible** – Leverage orchestration and migration tooling (e.g., RackWare workflows) to reduce manual effort and minimize human error.

- **Plan for post-migration optimization** – After workloads are stable in OCI, review performance, right-size compute shapes, and integrate with OCI's managed services (e.g., monitoring, security, backup, databases) to maximize efficiency and cost savings.

- **Prioritize security and compliance** – Ensure IAM policies, network security lists, encryption settings, and audit configurations are validated before workloads are exposed to production use.

- **Leverage OCI-native services** – Take advantage of OCI's managed services such as Autonomous Database, Object Storage, and monitoring services to modernize applications post-migration.

