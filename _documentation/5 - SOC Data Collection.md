# 5 - SOC Environment Data Collection

The Kali Purple virtual SOC will be primarily composed of 4 nodes: a firewall, a SIEM, a threat intelligence/vulnerability scanning platform, and a network traffic analysis platform. The current instructions provided with the Kali Purple SOC guide is not to clear as to what order to deploy/configure the nodes. In this and related documents, I intend to clarify how and what order to deploy the nodes for the SOC to operate appropriately.

I will include some optional information for building forward network sensors, as well as SOC analyst workstations.

## 5.1 - SOC Node Build Order

Based on my experimentation with deploying this virtual SOC solution according to the guidelines provided by the Kali Purple community, through trial-and-error I have devised what I believe to be (a) logical order of deployment. The order in which the virtual nodes will be built shall be as follows:

1. PROTECT Node (SOC Enclave Firewall)
2. DETECT Node (Elastic Stack SIEM)
3. IDENTIFY Node (Threat Intelligence, Vulnerability Scanner)
4. RESPOND Node (Network Analysis Aggregation Node)
5. (OPTIONAL) Remote Sensor (Forward Network Sensor, IDS)
6. (OPTIONAL) SOC Console (SOC Analyst Workstation)

## 5.2 - Network Management

You can use this table as a guide for configuring your own network layout. Here are the network configurations I will use for each node:

| **Hostname** | **CSF Function** | **Interface** | **IP Address** | **Mask Length** | **Network** |
| --- | --- | --- | --- | --- | --- |
| **byzantium** | PROTECT | vmx0<p>vmx1<p>opt1<p>opt2 | DHCP<p>192.168.30.1<p>192.168.32.1<p>10.20.30.250 | DHCP<p>24<p>24<p>24 | WAN<p>SOC MGMT<p>SOC OPNET<p>SOC DMZ |
| **kali-purple** | DETECT | ens192 | 192.168.30.5 | 24 | SOC MGMT |
| **kali-violet** | IDENTIFY | ens192 | 192.168.30.7 | 24 | SOC MGMT |
| **kali-eminence** | RESPOND | ens192 | 192.168.30.3 | 24 | SOC MGMT |
| **kali-sensor-0x** | RESPOND | ens192<p>ens224 | 192.168.30.2x<p>\<promiscuous\> | 24<p>N/A | SOC MGMT<p>SPAN |
| **soc-\<role\>-xx** | ANY | ens192 | DHCP/192.168.32.x | 24 | SOC OPNET |

**Table 7 – Virtual Machine Networking Configuration**

Plan your network in a way that makes sense for your environment. Identify ranges of addresses to be assigned, methods of assignment (static, DHCP, etc.), and virtual networks that will be associated with each range if necessary.
