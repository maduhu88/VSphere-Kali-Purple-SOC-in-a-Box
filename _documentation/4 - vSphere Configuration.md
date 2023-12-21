# VMWare vSphere Configuration

## Licensing Requirements (Remote Sensor deployments only)

In this vSphere deployment, the vSphere Distributed Switch (VDS) can be utilized. This allows for, among other things, port mirroring sessions to be created. If you are not implementing Remote Sensors, then you can still follow the remaining steps using the vSphere Standard Switch (VSS), which is free to use. The VDS requires either an Enterprise Plus or vSAN license, and a VMware vCenter deployment.

## Virtual Switch Configuration

In a standard deployment, the built-in VSS will work fine for your virtual networking needs. However, if you choose to deploy remote sensors within your virtual environment, then you need to deploy a VDS.

### vSphere Standard Switch

In a single-host environment, the built-in standard virtual switch (VSS) will suffice for this SOC build (sans remote sensors). With the VSS setup, there is no need for any advanced network features, such as VLAN tagging. All port groups called for in this build can be built on the same VSS.

If you have a multi-host environment (without enterprise licensing), then it would be ideal for at least one of your physical NICs to be connected to a trunked port, and that your port groups be VLAN-tagged. This would facilitate VMs on separate hosts but within the same VLAN to communicate with each other.

### vSphere Distributed Switch

If you are fortunate enough to have access to enterprise licensing (Enterprise Plus or vSAN), the Virtual Distributed Switch (VDS) offers a particular feature that can really enhance a cybersecurity lab environment: Port Mirroring. This feature is flexible enough to target individual virtual interfaces or even VLANs. The port mirroring feature does not extend to the standard switch or its port groups.

I have a multi-host setup (one server hosting target/attack systems, one hosting the SOC) connected via VDS. Each port group is assigned a VLAN ID. I have one port group dedicated as a SPAN network. The only interfaces that would be connected to it would be the monitoring interfaces of sensors. The security settings of the SPAN network allows Promiscuous mode, MAC address changes, and Forged transmits. As far as it's VLAN settings, the VDS allows the following: None, VLAN (ID), Private VLAN, and VLAN Trunking (range). I have the SPAN network set for trunking, with the range set for the IDs I wish to monitor.

The physical uplinks (one per server) for the VDS are connected to a trunked port, configured to pass all VLANs that have been assigned to port groups (standard or distributed). No mirroring/spanning configuration is needed on the physical switch. This is done on the VDS, with my sources being the VLANs I want to monitor, and the destination being the specific interface on the target sensor (typically its secondary interface).

## Virtual Networks

The names and assigned IP subnet configuration of the virtual networks listed below can be whatever you want in your environment.

### VM Network (Standard Port Group)

This network is for management access to your hypervisor. This should be connected to the VSS that has vnic0 as its uplink.

### SOC MGMT (Standard or Distributed Port Group)

There must be a network that is intended to be an internal, protected network. This is the network where the IDENTIFY, DETECT, and RESPOND nodes will reside. If any other services are developed to be a part of the SOC, they can also reside in this network. Within this documentation, this network will be referred to as "SOC MGMT" or "MGMT," and its IP subnet will be 192.168.30.0/24.

### SOC DMZ (Standard or Distributed Port Group)

This network will host any networks/hosts that will be monitored/defended by this SOC solution. Within this documentation, this network will be referred to as "SOC DMZ" or "DMZ," and its IP subnet will be 10.20.30.0/24.

### SOC OPNET (Standard or Distributed Port Group) (optional)

This network is intended to be the SOC operations network, hosting SOC analyst consoles. This documentation will refer to this network as "SOC OPNET" or "OPNET," and its IP subnet will be 192.168.32.0/24.

### SOC WAN (Standard or Distributed Port Group)

This network's function is primarily to provide access to the Internet for hosts behind the SOC firewall. The corresponding network interface of the firewall can be connected to any existing virtual network that has a path to the Internet. **INTERNET ACCESS IS REQUIRED TO COMPLETE SETUP OF THE SOC SOLUTION.**

### SPAN (Distributed Port Group) (Remote Sensor Deployment only)

This network will provide connectivity to any remote sensor's monitoring interface(s). As this network will be configured with port mirroring, it must be a Distributed Port Group on a vSphere Distributed Switch.

## VM Hardware Profiles

\*- Minimum values for memory and CPU cores are pulled from the Proxmox deployment documentation.

### PROTECT Node Hardware

| Minimum CPU Cores\* | Minimum Memory\* | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 2 | 2 GB | SOC Enclave Firewall, SOC Internal DNS Resolver, Proxy Server, IDS/IPS | 1 x 128 GB | 4 | **WAN:** DHCP<p>**MGMT:** 192.168.30.1/24<p>**DMZ:** 10.20.30.250/24<p>**OPNET:** 192.168.32.1/24 |

**Table 2 – PROTECT Node Hardware Configuration**

### DETECT Node Hardware

| Minimum CPU Cores\* | Minimum Memory\* | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 4 | 20 GB | Elastic SIEM | 1 x 2 TB | 1 | **MGMT:** 192.168.30.5/24 |

**Table 3 – DETECT Node Hardware Configuration**

### IDENTIFY Node Hardware

| Minimum CPU Cores\* | Minimum Memory\* | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 4 | 8 GB | Threat Intelligence, Vulnerability Scanner | 1 x 128 GB | 1 | **MGMT:** 192.168.30.7/24 |

**Table 4 – IDENTIFY Node Hardware Configuration**

### RESPOND Node Hardware

| Minimum CPU Cores\* | Minimum Memory\* | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 6 | 16 GB | Network Traffic Analysis | 1 x 512 GB | 1 | **MGMT:** 192.168.30.3/24 |

**Table 5 – RESPOND Node Hardware Configuration**

---
<div align="center">
| [Previous Section: 3 - Software Requirements](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/3%20-%20Software%20Requirements.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 5 - SOC Environment](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md |
</div>