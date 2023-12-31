# 6 - Building the Nodes in vSphere

In this section, we will build the 4 virtual machines that will make up the core of the SOC. These steps will be accomplished within vSphere. The OS for the PROTECT node will be OPNsense, and Kali Purple will be installed on the remaining nodes, DETECT, IDENTIFY, and RESPOND.

<details>
<summary><h2>6.1 - vSphere: Building the PROTECT Node</h2></summary>

We will start with the NIST CSF PROTECT node. For the purposes of this document, I will use the naming conventions referred to in the original Proxmox instructions from the Offensive Security Kali Linux community. The node in this section will be named "byzantium.kali.purple." This will be the enclave firewall. It will provide boundary protection, transparent HTTP proxy, DNS, and port forwarding services to the internal nodes of the SOC.

**NOTE 1** : You will need to have a system (physical or virtual) that will be able to access the firewall from its LAN (in my case, "SOC MGMT") interface to accomplish the web components of firewall configuration.

**NOTE 2** : One of the changes I made to the steps in this section was to consolidate all firewall modifications called for in the entire set of Proxmox instructions into this section, to eliminate the need for revisiting this node each time you are setting up another component of the SOC.

**NOTE 3** : As of the writing of this document, I have been unable to successfully compile Filebeat on OPNsense 23.7, so there will be no information regarding this process at this time.

**NOTE 4** : I have created enclaves and firewall rules that are not present in the Proxmox instructions. These are due to modifications I have made in this deployment (namely adding an enclave for analyst operations and preparing to receive external Elastic Agent traffic via the DMZ). Also, since I am not using a micro-segmented architecture, I will not configure any sub-interfaces. All rules meant for a sub-interface from the Proxmox instructions will be configured on the "SOC MGMT" interface in this setup. You will need to have a system (physical or virtual) that will be able to access the firewall from its LAN (in my case, "SOC MGMT") interface to accomplish the web components of firewall configuration.

**1. In the vSphere web interface, click "Create/Register VM…"** 

![](_images/1._vSphere_-_New_VM.png)

Figure 1 – PROTECT – VMware vSphere: New VM in vSphere


**2. Select "Create a new virtual machine."**

![](_images/2._vSphere_-_New_VM_-Create_New_VM_.png)

Figure 2 – PROTECT – VMware vSphere: Create a new virtual machine


**3. At the "Select a name and guest OS" screen, enter "byzantium" as the name. For guest OS family choose "Linux." Select the highest available 64-bit Debian GNU version.** 

![](_images/3._vSphere_-_Create_New_VM_-_Name_VM_and_Guest_OS.PNG)

Figure 3 – PROTECT – VMware vSphere: VM name and guest OS


**4. At the "Select storage" screen, choose your desired available datastore.**

![](_images/4._vSphere_-_New_VM_-_Select_Storage.png)

Figure 4 – PROTECT – VMware vSphere: Select datastore


**5. At the "Customize settings" screen, modify the VM settings in line with the requirements of this node: 2 CPUs, 2 GB RAM, 1 x 128 GB HDD, 3 NICs (OPNET NIC is optional). Ensure that the WAN interface is connected to a network that has Internet connectivity (and preferably DHCP, though not necessary).** 

![](_images/5._vSphere_-_New_VM_-_VM_Configuration.PNG)

Figure 5 – PROTECT – VMware vSphere: Customize settings


**6. Configure the hard drive for thin provisioning.** 

![](_images/6._vSphere_-_New_VM_-_Disk_Info.png)

Figure 6 – PROTECT – VMware vSphere: Customize hard disk settings


**7. For the CD/DVD, choose "Datastore ISO file" and navigate to your OPNsense ISO within your datastore and click "Select." Ensure that the ISO is configured to "Connect at power on."** 

![](_images/7._vSphere_-_New_VM_-_Select_ISO.png)


Figure 7 – PROTECT – VMware vSphere: ISO selection 

![](_images/8._byzantium_settings.PNG)

Figure 8 – PROTECT – VMware vSphere: Connect CD/DVD Drive at power on

**Click "Finish" to create the virtual machine.**
</details>

<details>
<summary><h2>6.2 - vSphere: Building the DETECT Node</h2></summary>

In this section, we will build the heart of this SOC solution, the Elastic SIEM node. In line with the Proxmox instructions, we will name this node "kali-purple." Since all other nodes will be sending data to the Elasticsearch instance that will reside on this system, I chose to build it second, after the firewall. If you recall, the firewall (byzantium) is already configured to forward syslog data to this node. Once we get this node configured, it should automatically start ingesting the log data from the firewall.

**To build the virtual machine for the DETECT node, use the steps in [Section 6.1](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/6%20-%20Building%20the%20vSphere%20Nodes.md#61-vsphere-building-the-protect-node) to create a new VM in vSphere, coupled with the data from [Table 2 (Section 4.4.2)](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/4%20-%20vSphere%20Configuration.md#442-detect-node-hardware). We will be using the Kali Purple 2023.3 ISO for the operating system in this section (Figure 9). Your resulting VM should appear as in Figure 10.**

![](_images/9._vSphere_-_New_VM_-_Select_ISO.png)

Figure 9 – DETECT – VMware vSphere: Kali Purple ISO

![](_images/10._vSphere_-_New_VM_-_VM_Settings.png)

Figure 10 – DETECT – VMware vSphere: VM Settings
</details>

<details>
<summary><h2>6.3 - vSphere: Building the IDENTIFY Node</h2></summary>

**As with the DETECT node, we will use the steps in [Section 6.1](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/6%20-%20Building%20the%20vSphere%20Nodes.md#61-vsphere-building-the-protect-node) to create a new VM in vSphere, coupled with the data from [Table 2 (Section 4.4.3)](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/4%20-%20vSphere%20Configuration.md#443-identify-node-hardware). We will also be using the Kali Purple 2023.3 ISO for the operating system in this section. Your resulting VM should appear as in Figure 11.**

![](_images/11._vSphere_-_New_VM_-_VM_Settings.png)

Figure 11 – IDENTIFY – VMware vSphere: VM Settings
</details>

<details>
<summary><h2>6.4 - vSphere: Building the RESPOND Node</h2></summary>

**As with the IDENTIFY/DETECT nodes, we will use the steps in [Section 6.1](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/6%20-%20Building%20the%20vSphere%20Nodes.md#61-vsphere-building-the-protect-node) to create a new VM in vSphere, coupled with the data from [Table 2 (Section 4.4.4)](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/4%20-%20vSphere%20Configuration.md#444-respond-node-hardware). Your resulting VM should appear as in Figure 12.**

![](_images/12._vSphere_-_New_VM_-_VM_Settings.png)

Figure 12 – RESPOND – VMware vSphere: VM Settings
</details>

---
<div align="center">
| [Previous Section: 5 - SOC Environment](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/5%20-%20SOC%20Data%20Collection.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/README.md) | [Next Section: 7 - Configure PROTECT node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/7%20-%20Configuring%20the%20PROTECT%20node.md) |
</div>