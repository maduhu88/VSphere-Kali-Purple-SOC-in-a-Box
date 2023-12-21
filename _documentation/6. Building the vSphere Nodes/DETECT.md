## vSphere: Building the DETECT Node

In this section, we will build the heart of this SOC solution, the Elastic SIEM node. In line with the Proxmox instructions, we will name this node "kali-purple." Since all other nodes will be sending data to the Elasticsearch instance that will reside on this system, I chose to build it second, after the firewall. If you recall, the firewall (byzantium) is already configured to forward syslog data to this node. Once we get this node configured, it should automatically start ingesting the log data from the firewall.

**To build the virtual machine for the DETECT node, use the steps in Section 6.1 to create a new VM in vSphere, coupled with the data from Table 2 (Section 4.4.2). We will be using the Kali Purple 2023.3 ISO for the operating system in this section (Figure 9). Your resulting VM should appear as in Figure 10.**

![](_images/9._vSphere_-_New_VM_-_Select_ISO.png)

Figure 9 – DETECT – VMware vSphere: Kali Purple ISO

![](_images/10._vSphere_-_New_VM_-_VM_Settings.png)

Figure 10 – DETECT – VMware vSphere: VM Settings