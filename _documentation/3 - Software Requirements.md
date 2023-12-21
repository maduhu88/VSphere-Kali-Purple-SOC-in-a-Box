# Software Requirements

## VMWare vSphere

### Minimum Version

The minimum version of VMware vSphere (ESXi) these instructions have been tested on is vSphere 6.7. The minimum version of VMware vCenter that was tested is vCenter 8.0.0, though at least 7.x should work.

### Recommended Version

The recommended version of VMware vSphere and vCenter for these instructions is vSphere/vCenter 7.0.3 or later. As a reminder, vCenter is only required if you desire to configure remote sensors.

### Source

[https://customerconnect.vmware.com/downloads/info/slug/datacenter\_cloud\_infrastructure/vmware\_vsphere\_hypervisor\_esxi/7\_0](https://customerconnect.vmware.com/downloads/info/slug/datacenter_cloud_infrastructure/vmware_vsphere_hypervisor_esxi/7_0)

  1.
## OPNsense Firewall

In the realm of the SOC, the OPNsense firewall distribution plays a critical role as the boundary defender. OPNsense is a robust and open-source firewall solution known for its versatility and security features. It functions as the guardian of your SOC, safeguarding the perimeter, controlling traffic flows, and enforcing security policies. With its advanced capabilities, including intrusion detection and prevention, VPN support, and traffic filtering, OPNsense ensures that your SOC's external boundaries remain fortified, providing essential protection against external threats while allowing for controlled, secure communication with the outside world.

### Version Differences

As of the writing of this document, the version of OPNsense used in the Proxmox instructions is OPNsense 22.7.

### Minimum Version

The minimum version of OPNsense these instructions have been tested on is OPNsense 23.7.

### Recommended Version

The recommended version of OPNsense for these instructions is OPNsense 23.7 or later.

### Source

[https://opnsense.org/download/](https://opnsense.org/download/)

## Kali Purple Linux Distribution

Kali Purple, stemming from the renowned Kali Linux lineage, offers an exceptional choice as the baseline operating system for deploying a virtual Security Operations Center (SOC). With a comprehensive suite of over 100 defensive tools, streamlined documentation, pre-generated images, and automated attack capabilities through Kali Autopilot, it simplifies setup and configuration. Its alignment with NIST CSF guidelines ensures efficient SOC operations, making it an ideal foundation for your virtual SOC deployment. This distribution will be the base operating system for the IDENTIFY, DETECT, and RESPOND nodes, as well as any SOC analyst workstations you may wish to deploy.

### Version Differences

As of the writing of this document, the version of Kali used in the Proxmox instructions is Kali 2022.4/2023.1.

### Minimum Version

The minimum version of Kali Purple Linux these instructions have been tested on is 2023.3.

### Recommended Version

The recommended version of Kali Purple Linux for these instructions is 2023.3 or later.

### Source

[https://cdimage.kali.org/kali-2023.3/kali-linux-2023.3-installer-purple-amd64.iso](https://cdimage.kali.org/kali-2023.3/kali-linux-2023.3-installer-purple-amd64.iso)

## Hedgehog Linux Distribution

Hedgehog Linux, a Debian-derived operating system, specializes in network monitoring, packet capture to PCAP files, identification of file transfers within network traffic, and subsequent threat scanning of these files. Moreover, it excels at producing and transmitting essential data, including Zeek logs, Arkime sessions, and other critical information, directly to the Malcolm platform. When seamlessly integrated into a virtual SOC, Hedgehog Linux serves as an invaluable remote network sensor, enhancing threat detection and response capabilities by actively monitoring and analysing network activity. This distribution will the base operating system for any remote network sensors you decide to deploy.

### Minimum Version

The minimum version of Hedgehog Linux these instructions have been tested on is 23.10.0.

### Recommended Version

The recommended version of Hedgehog Linux for these instructions is 23.12.0 or later.

### Source

[https://malcolm.fyi/iso/hedgehog-23.12.0.iso](https://malcolm.fyi/iso/hedgehog-23.12.0.iso)

---
## [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md)
## [Previous Section: 2 - Key Differences between Proxmox and vSphere SOC deployments](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/2%20-%20Differences.md)
## [Next Section: 4 - VMware vSphere Configuration](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/4%20-%20vSphere%20Configuration.md)