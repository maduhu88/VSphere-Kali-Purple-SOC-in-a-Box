# 1 - Introduction
# 2 - Key Differences between Proxmox and vSphere SOC deployments
  *  Hypervisor Changes
  *  No Micro-segmentation
  *  Software Versions
  *  Network Architecture Revisions
  *  Remote Sensor Integration
# 3 - Software Requirements
  * VMWare vSphere
  * OPNsense Firewall
  * Kali Purple Linux
  * Hedgehog Linux
# 4 - VMware vSphere Configuration
## 4.1 - Licensing Requirements (Remote Sensor deployments only)
## 4.2 - Virtual Switch Configuration
## 4.3 - Virtual Networks
## 4.4 - Virtual Machine Hardware Profiles
# 5 - SOC Environment Data Center
## 5.1 - SOC Node Build Order
## 5.2 - Network Management
# 6 - Building the nodes in vSphere
## 6.1 - vSphere: Building the PROTECT node
## 6.2 - vSphere: Building the DETECT node
## 6.3 - vSphere: Building the IDENTIFY node
## 6.4 - vSphere: Building the RESPOND node
# 7 - Configure: PROTECT Node
## 7.1 - OPNsense Installation
## 7.2 - OPNsense Configuration
# 8 - Kali Purple
## 8.1 - Kali Purple Installation
## 8.2 - Kali Purple Configuration
# 9 - Configure: DETECT Node
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
# 10 - Configure: IDENTIFY Node
# 11 - Configure: RESPOND Node
# 12 - Build and Configure: Remote Network Sensor
# 12 - Build and Configure: SOC Analyst Workstation
