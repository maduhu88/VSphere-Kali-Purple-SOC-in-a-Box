# 1 - Introduction

In this documentation, I delve into the deployment of a Kali Purple-based Security Operations Center (SOC) in a virtualized environment using VMware vSphere. Unlike the existing guidelines using Proxmox (010\_10: Kali Purple SOC IAB Overview, n.d.), this guide offers a tailored approach for vSphere users, highlighting key differences and unique challenges encountered. I focus on adapting the deployment strategies to fit vSphere's virtualization framework, ensuring that you can leverage Kali Purple's robust cybersecurity tools within this widely used hypervisor.

Within this documentation, I will cover the following:

1. Key Differences between the Proxmox version of the SOC deployment and this vSphere deployment
2. Software Requirements
3. VMware vSphere configuration
4. Baseline Build: PROTECT node (byzantium)
5. Baseline Build: DETECT node (kali-purple)
6. Baseline Build: IDENTIFY node (kali-violet)
7. Baseline Build: RESPOND node (kali-eminence)
8. Baseline Build: SENSOR node (INL Malcolm Hedgehog Linux)
9. Baseline Build: SOC Analyst workstation (Kali Purple build)

This document is not meant to recommend vSphere over Proxmox. I stress that you, the environment engineer, can and should use whichever hypervisor you are most comfortable with. Outside of hypervisor-specific instructions (e.g. building the VMs), all other instructions can be accomplished regardless of the hypervisor present.

---
<div align="center">
| [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Section 2 - Key Differences from OffSec](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/2%20-%20Differences.md) |
</div>