# 2 - Key Differences between Proxmox and vSphere SOC deployments

## 2.1 - Hypervisor Changes

I chose VMware vSphere simply because it's the hypervisor I already have deployed in my lab environment. I am certain that Proxmox was chosen by the folks at Offensive Security due to it being among the more popular open-source hypervisors available, with many of the enterprise features of VMware.

## 2.2 - No Micro-segmentation

I opted against micro-segmentation initially for ease of deployment. I may revisit this. In the Proxmox instruction, the internal nodes are configured with /31 subnet masks, each having a separate gateway (all of which are virtual Ips on the LAN interface of the firewall). Each of these micro-subnets have a different VLAN ID. I have chosen to use a single /24 subnet mask for the LAN interface, and all nodes (IDENTIFY, DETECT, and RESPOND) lie therein.

## 2.3 - Software Versions

As of the writing of this document, several versions of the software used in the Proxmox version of the SOC deployment have been updated. In my instructions, I will utilize all the current versions of each item that is available at the time of draft.

| **Software Title** | **Proxmox Version** | **Current Version** | **My Version** |
| --- | --- | --- | --- |
| **OPNsense Firewall** | 22.7 | 23.7 | 23.7 |
| **Kali Purple Linux** | 2022.4/2023.1 | 2023.4 | 2023.4 |
| **Elasticsearch** | 8.6.1 | 8.11.3 | 8.11.3 |
| **Kibana** | 8.6.1 | 8.11.3 | 8.11.3 |
| **Elastic Agent** | 8.6.1 | 8.11.3 | 8.11.3 |
| **Metricbeat** | 8.6.1 | 8.11.3 | 8.11.3 |
| **Filebeat** | 8.6.1 | 8.11.3 | 8.11.3 |
| **INL Malcolm** | 23.01.0 | 23.12.0 | 23.10.0 |
| **OpenCTI** | 5.5.2 | 5.12.9 | 5.12.8 |

**Table 1 – Software Differences**

## 2.4 - Network Architecture Revisions

As laid out, the suggested architecture seems a bit incomplete in my opinion. This assumption comes when including the build guidance that was included. The RESPOND node has many tools that you would typically find on a network sensor/IDS/IPS, but as built, it appears that the only way it can see any network data is if you manually upload PCAP to it directly. It has no monitoring interface.

Initially, to transform this "static" SOC into a live dynamic one, I thought of two solutions. First, add a monitoring interface to the RESPOND node. Second (my preferred method), build forward network sensors that monitor the network, and feed data back to the RESPOND node. This second option will be implemented in this draft.

 I included an extra enclave for SOC analyst consoles. These systems could also be deployed within the same network as the IDENTIFY/DETECT/RESPOND nodes if desired.

## 2.5 - Remote Sensor Integration

I added steps to build and integrate remote sensors, based on the Hedgehog Linux distribution, a companion to Malcolm recommended by Idaho National Labs (INL), the maintainers of the Malcolm Network Traffic Analysis platform. This can seamlessly integrate with the Malcolm instance configured on the RESPOND node. The inclusion of the network sensors is completely optional. As of the writing of this guide, I am unaware of any port mirroring/spanning capability within Proxmox.

**NOTE:** To utilize the remote network sensors, you must have either a vSphere Enterprise Plus or vSAN license, and VMware vCenter as the steps involve creating and configuring a vSphere Distributed Switch with Port Mirroring capabilities.

---
## [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md)
## [Previous Section: Introduction](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/1%20-%20Introduction.md)
## [Next Section: 3 - Software Requirements](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/3%20-%20Software%20Requirements.md)