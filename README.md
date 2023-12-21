# 1 - [Introduction](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/1%20-%20Introduction.md)
# 2 - [Key Differences between Proxmox and vSphere SOC deployments](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/2%20-%20Differences.md)
# 3 - [Software Requirements](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/3%20-%20Software%20Requirements.md)
# 4 - [VMware vSphere Configuration](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/4%20-%20vSphere%20Configuration.md)
# 5 - [SOC Environment Data Collection](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md)
# 6 - [Building the nodes in vSphere](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/6%20-%20Building%20the%20vSphere%20Nodes.md)
## 6.1 - vSphere: Building the PROTECT node
## 6.2 - vSphere: Building the DETECT node
## 6.3 - vSphere: Building the IDENTIFY node
## 6.4 - vSphere: Building the RESPOND node
# 7 - CONFIGURE: PROTECT Node
## 7.1 - OPNsense Installation
## 7.2 - OPNsense Configuration
# 8 - Kali Purple
## 8.1 - Kali Purple Installation
## 8.2 - Kali Purple Configuration
# 9 - CONFIGURE: DETECT Node
## 9.1 - Elastic Stack SIEM Installation and Configuration
### 9.1.1 - Install Elasticsearch
### 9.1.2 - Install Kibana
### 9.1.3 - Enable HTTPS for Kibana
## 9.2 - Fleet Server
### 9.2.1 - Installation
### 9.2.2 - Linux Server Policy
## 9.3 - Beats
### 9.3.1 - Collect Elasticsearch Certificate Fingerprint
### 9.3.2 - Metricbeat Installation and Configuration
### 9.3.3 - Filebeat Installation and Configuration
# 10 - CONFIGURE: IDENTIFY Node
## 10.1 - Elastic Agent
### 10.1.1 - Installation
## 10.2 - Docker
### 10.2.1 - Installation
### 10.2.2 - Portainer
## 10.3 - Greenbone Vulnerability Management
### 10.3.1 - Installation and configuration
## 10.4 - OpenCTI
### 10.4.1 - Installation
### 10.4.2 - Enable HTTPS
### 10.4.3 - Add Connection
# 11 - CONFIGURE: RESPOND Node
## 11.1 - Elastic Agent
## 11.2 - Docker and Malcolm Prerequisites
### 11.2.1 - Installation
### 11.2.2 - Portainer
## 11.3 - INL Malcolm
### 11.3.1 - Clone Malcolm Repository
### 11.3.2 - Configure Malcolm
### 11.3.3 - Pull Malcolm Docker Images
### 11.3.4 - Start Malcolm
### 11.3.5 - Verify Malcolm Web Services
### 11.3.6 - Create Sensor Account (only if deploying remote sensors)
# 12 - Build and Configure: Remote Network Sensor
## 12.1 - VMware vSphere: VM Configuration
## 12.2 - Hedgehog linux Installation
## 12.3 - Sensor Configuration
### 12.3.1 - CONFIGURE: Hostname and Interfaces
### 12.3.2 - Filebeat Certificate Transfer
### 12.3.3 - CONFIGURE: Capture and Forwarding
### 12.3.4 - Reboot Sensor
### 12.3.5 - Verify Sensor Connectivity
# 13 - Build and Configure: SOC Analyst Workstation
## 13.1 - VMware vSphere: VM Configuration
## 13.2 - Kali Purple Installation and Configuration
