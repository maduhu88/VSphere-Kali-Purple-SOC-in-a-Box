# 1. Introduction
# 2. Key Differences between Proxmox and vSphere SOC deployments
  *  Hypervisor Changes
  *  No Micro-segmentation
  *  Software Versions
  *  Network Architecture Revisions
  *  Remote Sensor Integration
# 3. Software Requirements
  * VMWare vSphere
  * OPNsense Firewall
  * Kali Purple Linux
  * Hedgehog Linux
# 4. VMware vSphere Configuration
## Licensing Requirements (Remote Sensor deployments only)
## Virtual Switch Configuration
## Virtual Networks
## Virtual Machine Hardware Profiles
# 5. SOC Environment Data Center
## SOC Node Build Order
## Network Management
# 6. Building the nodes in vSphere
## vSphere: Building the PROTECT node
## vSphere: Building the DETECT node
## vSphere: Building the IDENTIFY node
## vSphere: Building the RESPOND node
# 7. Configure: PROTECT Node
## OPNsense Installation
## OPNsense Configuration
# 8. Kali Purple
## Kali Purple Installation
## Kali Purple Configuration
# 9. Configure: DETECT Node
## Elastic Stack SIEM Installation and Configuration
### Install Elasticsearch
### Install Kibana
### Enable HTTPS for Kibana
## Fleet Server
### Installation
### Linux Server Policy
## Beats
### Collect Elasticsearch Certificate Fingerprint
### Metricbeat Installation and Configuration
### Filebeat Installation and Configuration
# 10. Configure: IDENTIFY Node
# 11. Configure: RESPOND Node
# 12. Build and Configure: Remote Network Sensor
# 12. Build and Configure: SOC Analyst Workstation
