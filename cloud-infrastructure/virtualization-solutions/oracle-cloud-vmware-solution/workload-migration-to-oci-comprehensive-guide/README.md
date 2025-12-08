# Workload Migration to OCI Comprehensive Guide

This guide provides a detailed technical overview of migration methodologies and tooling — including VMware HCX, RackWare, and Oracle Cloud Migrations (OCM) — used to transition workloads from both VMware and non-VMware environments to Oracle Cloud Infrastructure (OCI). It outlines key requirements, tool capabilities, architectural approaches, and decision frameworks to support large-scale enterprise migrations with minimal disruption.

Reviewed: 08.12.2025

## On-premises migration scenarios overview

Select the appropriate migration guide based on your source environment and target platform:

| Source Environment | Target Platform | Migration Tooling | Migration Guide |
|-------------------|-----------------|--------------|-----------------|
| VMware vSphere | Oracle Cloud VMware Solution (OCVS) | VMware HCX | [VMware vSphere to OCVS using HCX](./files/vmware-vsphere-to-ocvs-hcx.md) |
| VMware vSphere | OCI Native Compute Instances | Oracle Cloud Migrations (OCM) / RackWare | [VMware vSphere to OCI Native](./files/vmware-vsphere-to-oci-native.md) |
| Microsoft Hyper-V / KVM | OCI Native Compute Instances | RackWare / HCX Enterprise (OSAM) | [Hyper-V/KVM to OCI Native](./files/hyper-v-kvm-to-oci-native.md) |
| Microsoft Hyper-V / KVM | Oracle Cloud VMware Solution (OCVS) | HCX Enterprise (OSAM) / RackWare | [Hyper-V/KVM to OCVS](./files/hyper-v-kvm-to-ocvs.md) |
| Physical x86 Servers | OCI Native Compute Instances / OCVS | RackWare | [Physical x86 to OCI](./files/physical-to-oci.md) |

## Cloud migration scenarios overview
| Source Environment       | Target Platform           | Migration Tooling                   | Migration Guide                              |
|-------------------------|---------------------------|-------------------------------|----------------------------------------------|
| AWS EC2 / VMs           | OCI Native Compute Instances | Oracle Cloud Migrations (OCM) / RackWare | [AWS to OCI Native](./files/aws-to-oci-native.md)           |
| Other Public Clouds (Azure, GCP, etc.) | OCI Native Compute Instances | RackWare | [Other Clouds to OCI Native](./files/other-clouds-to-oci-native.md) |



# When to use this asset?

Use this document when planning or executing migrations from on-premises or other cloud environments to OCI Native services or Oracle Cloud VMware Solution (OCVS). It covers virtualized, bare-metal, and mixed-environment scenarios.

# Instructions for Utilising This Asset

Use this guide as a reference and planning framework for OCI and OCVS migration projects. It includes decision diagrams, tool comparison, guidance and best practices. Each migration scenario has its own detailed guide linked in the table above.

# Conclusion

Migrating workloads to OCI requires a comprehensive assessment of the existing environment, target architecture design, and methodical execution. By following the approaches and best practices outlined in this guide, organizations can achieve a secure, efficient, and low-risk migration to Oracle Cloud Infrastructure.

# License

Copyright (c) 2025 Oracle and/or its affiliates.

Licensed under the Universal Permissive License (UPL), Version 1.0.

See [LICENSE](https://github.com/oracle-devrel/technology-engineering/blob/main/LICENSE.txt) for more details.
