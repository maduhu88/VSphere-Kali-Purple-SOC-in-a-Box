# 7 - Configure: PROTECT Node

## 7.1 - OPNsense Installation

**1. In vSphere, right click on the "byzantium" virtual machine, select Power -\> Power On. You'll see a green arrow on the VM icon to show that it's running.**
      
![](_images/13._Power_Navigation.PNG)

*Figure 13 – VMware vSphere: Power on PROTECT VM*

**2. After the machine is powered on, right click the VM and select "Edit settings." Take note of the MAC addresses for each interface, and which network you assigned to them (particularly the WAN and LAN).**

![](_images/14._MAC_Addresses.PNG)

*Figure 14 – PROTECT – VMware vSphere: VM Network Interfaces*

**3. Click the VM's icon. On the next page, click the miniature console window to expand the console of the VM. Clicking within the window that appears will change focus to the VM.**

![](_images/15._OPNsense_-_Login.png)

*Figure 15 – PROTECT – OPNsense: Console login*

**4. Log into the VM with the username "installer", and password "opnsense."**

**5. Accept the default keymap.**

![](_images/16._OPNsense_-_Keymap.png)

*Figure 16 – PROTECT – OPNsense: Setup keymap*

**6. On the next screen, choose "Install (UFS)."**

![](_images/17._OPNsense_-_Installation_Options.png)

*Figure 17 – PROTECT – OPNsense: Install options*

**7. On the next screen, choose the option labelled "da0 \<VMware Virtual disk xx\> (128 GB)."**

![](_images/18._OPNsense_-_UFS_Configuration.png)

*Figure 18 – PROTECT – OPNsense: Target disk*

**8. On the following screen, choose "Yes" to continue with an 8GB swap partition.**

![](_images/19._OPNsense_-_UFS_Configuration_2.png)

*Figure 19 – PROTECT – OPNsense: Swap size*

**9. On the following screen, choose "Yes" to commit changes to the hard disk.**

![](_images/20._OPNsense_-_UFS_Configuration_3.png)

*Figure 20 – PROTECT – OPNsense: Confirm settings*

**10. The system will now install OPNsense onto the disk. This will take a few minutes.**

![](_images/21._OPNsense_-_Install.png)

*Figure 21 – PROTECT – OPNsense: Installation*

**11. After the install process is complete, select "Root Password" to set the password for the root account.**

![](_images/22._OPNsense_-_Final_Configuration_1.png)

*Figure 22 – PROTECT – OPNsense: Final configuration*

**12. Set a secure password for the root account. It will ask for you to confirm.**

![](_images/23._OPNsense_-_Set_Root_Password.png)

*Figure 23 – PROTECT – OPNsense: Set root password*

**13. Once you return to the final configuration screen after setting the root password, return to the vSphere interface and remove the ISO from the CD/DVD drive. Open the VM settings and select "Host device" for the CD/DVD drive and click "Save."**

![](_images/24._PROTECT_-_Remove_ISO.PNG)

*Figure 24 -PROTECT – VMware vSphere: Removal of ISO*

**14. Returning to the VM console, choose "Complete Install." This will reboot the VM.**
**15. When the login prompt appears, login with the root credentials you set previously. Verify that the interface designated as the WAN is the interface with a DHCP address assigned. On my VM, it is assigned to vmx1.**

![](_images/25._OPNsense_-_WAN_ID_and_root_login.png)

*Figure 25 – PROTECT – OPNsense: Root login*

**16. From the menu, select 1 to set interface assignments. Choose "n" for configuring LAGGs and VLANs.**

![](_images/26._OPNsense_-_Assign_Interfaces.png)

*Figure 26 – PROTECT – OPNsense: Assign interfaces*

**17. For the WAN interface name, enter the name of the interface that corresponds to the MAC address assigned to your WAN network. In my case, it is vmx1. Repeat this for the LAN network (mine is vmx2). The remaining interfaces can be assigned in any order. Use the data gathered from step 2 of this section. Confirm your settings.**

![](_images/27._OPNsense_-_Assign_Interfaces_-_WAN_and_LAN.png)

*Figure 27 – PROTECT – OPNsense: Assign interfaces (continued)*

**18. At the main menu, select 2 to set an IP address for an interface, and select the corresponding number for the LAN interface.**

![](_images/28._OPNsense_-_Change_LAN_IP.png)

*Figure 28 – PROTECT – OPNsense: Setting LAN IP*

**19. Choose "N" for interface configuration via DHCP. Enter the desired IP address for your LAN interface and press ENTER. Choose "24" for the subnet mask bit count and press ENTER. Press ENTER for no upstream gateway. Select "n" for Ipv6 configuration via WAN tracking and DHCP6. Select "N" for configuring DHCP for the LAN interface.**

![](_images/29._OPNsense_-_Change_LAN_IP_2.png)

*Figure 29 – PROTECT – OPNsense: LAN IP configuration*

**20. Select "n" for changing the web GUI protocol from HTTPS to HTTP. Select "y" for generating a new self-signed certificate for the web GUI, as well as "y" for GUI access default restoral. Once this step completes, you will be able to access the firewall via HTTPS at the LAN IP address you configured.**

![](_images/30._OPNsense_-_Change_LAN_IP_3.png)

*Figure 30 – PROTECT – OPNsense: LAN IP configuration (continued)*

**21. At the main menu screen, choose 0 to log out of the firewall.**

## 7.2 - OPNsense Configuration

The following steps are to be accomplished from a separate system that has access to the LAN interface (in my case, the SOC MGMT interface) of the firewall.

### 7.2.1 - System Configuration

1. Browse to the LAN address of your firewall (https://\<LAN IP\>). You'll be greeted by a warning about your connection not being private. Accept the risks to continue to the site. You'll see the login portal of the OPNsense firewall. Log in with the root credentials you set during setup.

![](_images/31._OPNsense_-_Web_Portal.png)

*Figure 31 – PROTECT – OPNsense: Web Login Portal*

2. Upon first login, the Setup Wizard will launch. Click "Next." 

![](_images/32._OPNsense_-Setup_Wizard.png)

*Figure 32 – PROTECT – OPNsense: Setup Wizard*

3. On the "System: Wizard: General Information" screen, enter your desired hostname, domain name, and language preferences. The figure shown here uses the settings recommended in the Proxmox instructions. When done, click "Next." 

![](_images/33._OPNsense_-Setup_Wizard_-_General_Information.png)

*Figure 33 – PROTECT – OPNsense: Setup Wizard – General information*

4. On the next screen, you can leave the "Time server hostname" as default or change if you wish. Set the "Time zone" value to your time zone. When done, click "Next." 

![](_images/34._OPNsense_-Setup_Wizard_-_Time_Server_info.png)

*Figure 34 – PROTECT – OPNsense: Time server information*

5. On the next page, leave the WAN interface with default settings. Click "Next." 

![](_images/35._OPNsense_-Setup_Wizard_-_WAN_Interface.png)

*Figure 35 – PROTECT – OPNsense: WAN interface configuration*

6. On the next page, verify your LAN IP settings are correct, then click "Next." 

![](_images/36._OPNsense_-Setup_Wizard_-_LAN_Interface.png)

*Figure 36 – PROTECT – OPNsense: LAN interface configuration*

7. On the following page, verify your root password, then click "Next." 

![](_images/37._OPNsense_-_Setup_Wizard_-_Set_Root_Password.png)

*Figure 37 – PROTECT – OPNsense: Root password*

8. Click "Reload" to apply the settings.

9. Next, using the navigation options on the left side of the screen, navigate to Interfaces -\> Assignments. 

![](_images/38._OPNsense_-_Interfaces_-_Assignments.png)

*Figure 38 – PROTECT – OPNsense: Interface assignments*

10. Click each interface listed on the left side of the screen. With each interface, you can change the name of it by modifying the description. Do this for any interfaces you desire. You also can modify/verify the IP settings for each interface. When done with all interfaces, click "Apply" to apply changes. 
      
![](_images/39._OPNsense_-_Interfaces_-_Renaming.png)

*Figure 39 – PROTECT – OPNsense: Renaming interfaces*

![](_images/40._OPNsense_-_Interfaces_-_IP_Addressing.png)

*Figure 40 – PROTECT – OPNsense: Configuring interfaces*

11. Next, navigate to System -\> Settings -\> Administration. Change the TCP port to 8443 and ensure that the "Enable Secure Shell" option is selected. Click "Save" at the bottom of the screen when done.

![](_images/41._OPNsense_-System_Administration.png)

*Figure 41 – PROTECT – OPNsense: Administration settings*

12. Next, navigate to Services -\> Web Proxy -\> Administration. Select the "Forward Proxy" tab at the top of the page. Ensure the "Proxy interfaces" has just the LAN interface selected. Set the Proxy port as port 80. Ensure the "Enable Transparent HTTP Proxy" option is checked. Set the SSL Proxy port to 443. Click "Apply" when done. You'll be presented a link to click to return to the firewall management site. 

![](_images/42._OPNsense_-Web_Proxy.png)

*Figure 42 – PROTECT – OPNsense: Web proxy settings*

![](_images/43._OPNsense_-System_Administration_-_apply.png)

*Figure 43 – PROTECT – OPNsense: Web redirect*

13. Next will setup logging. Navigate to System -\> Settings -\> Logging/targets. Click the red "+" on the right side of the screen to create a new destination.

![](_images/44._OPNsense_-Syslog.png)

*Figure 44 – PROTECT – OPNsense: *Syslog*

14. Configure the following settings for log forwarding. Use Figure 39 for choice selection for "Applications." Click "Save" when complete, and "Apply" to apply the configuration. 
      
![](_images/45._OPNsense_-_Firewall_Aliases_-_Ports_-_Remote_Administration.png)

*Figure 45 – PROTECT – OPNsense: Syslog to Elasticsearch*

![](_images/46._Syslogs.PNG)

*Figure 46 – PROTECT – OPNsense: Syslog app logs*

### 7.2.2 - Updating OPNsense

Now we will update our OPNsense installation.

1. In the navigation pane on the left side of the screen, navigate to Lobby -\> Dashboard. Click on the "Click to view pending updates" link in the right pane. This will bring you to the "System: Firmware" screen. 

![](RackMultipart20231221-1-5l8g4e_html_87ab5b1ac3c5fbea.png)
2. A few seconds after the next screen appears, the system will display a message identifying updates to install. Click "Close." 

![](RackMultipart20231221-1-5l8g4e_html_7962bccd8a91292d.png)
3. Scroll to the bottom of the screen and click "Update." This will start the update process. Upon completion, the firewall will reboot, and you will be returned to the OPNsense login screen. Log back into the firewall with your root credentials. 

![](RackMultipart20231221-1-5l8g4e_html_c0b6f0e177487fc6.png)
4. Click on the "Click to view pending updates" link in the right pane. After a few seconds, you should see that no updates are needed. 

![](RackMultipart20231221-1-5l8g4e_html_86dc58eb529a0f52.png)
### 7.2.3 - Install VM Tools

1. On the "System: Firmware" screen, navigate to the "Plugins" tab. In the search bar of this screen, search for "os-vmware." Click the "+" on the right to install this plugin. This should take just a few seconds. 

![](RackMultipart20231221-1-5l8g4e_html_22347b685205c26b.png)
2. Navigate to Power -\> Reboot. Click "Yes" to reboot the firewall. When presented with the OPNsense login page, log in with your root credentials. 

![](RackMultipart20231221-1-5l8g4e_html_a2fc8cba6ed3c36.png)
### 7.2.4 - Unbound DNS

In this section, we will configure the Unbound DNS Resolver service. As all nodes behind this firewall will use it as their DNS server, any internal services (hosts) need to have static entries for resolution to be successful.

1. Using the site navigation in the left pane, navigate to Services -\> Unbound DNS -\> Overrides. 

![](RackMultipart20231221-1-5l8g4e_html_a30f3844a0e9474b.png)

Figure 47 – PROTECT – OPNsense: Unbound DNS

1. On the right side of this page, click the red "+" to add a new entry.
2. Using the data in Table 7 and Figure 41 as a guide, create entries for your IDENTIFY, DETECT, and RESPOND nodes. Feel free to modify names/Ips as needed. 

![](RackMultipart20231221-1-5l8g4e_html_c05fb52eb8c3a014.png)

Figure 48 – PROTECT – OPNsense: DNS override entry

| **Enabled** | **Host** | **Domain** | **Type** | **IP Address** | **Description** |
| --- | --- | --- | --- | --- | --- |
| **Checked** | kali-purple | kali.purple | A (Ipv4 address) | 192.168.30.5 | DETECT node |
| **Checked** | kali-violet | kali.purple | A (Ipv4 address) | 192.168.30.7 | IDENTIFY node |
| **Checked** | kali-eminence | kali.purple | A (Ipv4 address) | 192.168.30.3 | RESPOND node |

Table 8 – PROTECT – OPNsense: Unbound DNS entries

1. Click "Save" to save each entry. You can create other entries here as needed. When done, click "Apply" to apply the current settings.

### 7.2.5 - Firewall Aliases

1. Next you will configure firewall aliases. Navigate to Firewall -\> Aliases. On this page, click the "+" near the lower right of the page to add a new alias. 

![](RackMultipart20231221-1-5l8g4e_html_3ffdb7e662608737.png)

Figure 49 – PROTECT – OPNsense: Firewall aliases

1. The first set of aliases you are to configure are for ports. Configure 6 aliases with the data presented below. 

![](RackMultipart20231221-1-5l8g4e_html_cf2ef52fd9627661.png)

Figure 50 – PROTECT – OPNsense: New alias configuration

| **Enabled** | **Name** | **Type** | **Content** | **Description** |
| --- | --- | --- | --- | --- |
| **Checked** | ports\_admin | Port(s) | 22, 443, 3389, 8443 | Ports for remote administration |
| **Checked** | ports\_elastic | Port(s) | 5044, 5601, 8220, 9001, 9200, 9300 | ELK Stack ports |
| **Checked** | ports\_gvm | Port(s) | 9392 | Greenbone Vulnerability Management HTTP port |
| **Checked** | ports\_mail | Port(s) | 25, 110, 143, 465, 587, 993, 995 | Ports for mail servers |
| **Checked** | ports\_web | Port(s) | 80, 443 | Ports for web servers |
| **Checked** | ports\_opencti | Port(s) | 8080, 9443 | OpenCTI and Portainer HTTP/HTTPS ports |

Table 9 – PROTECT – OPNsense: Port alias configurations

1. The next set of aliases you are to configure are for networks. Configure 2 aliases as presented below. 

![](RackMultipart20231221-1-5l8g4e_html_f96c3c7995a98e6c.png)

![](RackMultipart20231221-1-5l8g4e_html_97c98825edceca1e.png)

Figure 51 – PROTECT – OPNsense: Network aliases

1. The final set of aliases you are to configure are for hosts. Configure 3 aliases as presented below. 

![](RackMultipart20231221-1-5l8g4e_html_2e5acbb0abbcde15.png) 
![](RackMultipart20231221-1-5l8g4e_html_2ff282ffaec0ee77.png)
![](RackMultipart20231221-1-5l8g4e_html_cf152bc145e5847d.png)

Figure 52 – PROTECT – OPNsense: Host aliases

### 7.2.16 - Firewall Rules

1. Next you will configure firewall rules. Navigate to Firewall -\> Rules -\> [LAN Interface]. On this page, click the "+" near the upper right of the page to add a new rule. 

![](RackMultipart20231221-1-5l8g4e_html_77d8fd741e52ade.png)

Figure 53 – PROTECT – OPNsense: Firewall rules

1. As seen in the figure below, configure 6 firewall rules using the aliases created previously. The rules listed in green are default rules that already exist. The rule highlighted in orange is only relevant if you elected to configure the OPNET network. To re-order the rules, select the check box next to the rule you want to move on the rules screen, then, on the right side of the rules table, click the left arrow for the rule you want your selected rule to sit above. After all changes have been made, click "Apply." 

![](RackMultipart20231221-1-5l8g4e_html_9c8dc4067d0cd4ad.png)

Figure 54 – PROTECT – OPNsense: LAN Firewall rules order

| **Action** | **Interface** | **Protocol** | **Source** | **Source Port** | **Destination** | **Destination Port** | **Description** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **PASS** | [LAN] | \* | \* | \* | This Firewall | \* | Allow any to this firewall |
| **PASS** | [LAN] | TCP | \* | \* | SSN | ports\_admin | Allow management of Secure Server Network |
| **PASS** | [LAN] | TCP | SOC\_OPNET Net | \* | detect\_servers | ports\_elastic | Allow Elastic traffic from OPNET |
| **PASS** | [LAN] | TCP | \* | \* | identify\_servers | ports\_opencti | Allow access to OpenCTI web interface |
| **BLOCK** | [LAN] | \* | \* | \* | RFC1918 | \* | Block any to Secure Server Network |
| **PASS** | [LAN] | Ipv4\* | [LAN] Net | \* | \* | \* | Default allow LAN to any rule |
| **PASS** | [LAN] | Ipv6\* | [LAN] Net | \* | \* | \* | Default allow LAN Ipv6 to any rule |
| **PASS** | [LAN] | TCP | [LAN] Address | \* | 127.0.0.1/24 | 3128 | Redirect traffic to proxy |

Table 10 – PROTECT – OPNsense: LAN Firewall rules settings

1. If you elected to deploy an OPNET network, configure the following rules for this interface: 

![](RackMultipart20231221-1-5l8g4e_html_2b8fbc6d22f14c1b.png)

Figure 55 – PROTECT – OPNsense: OPNET Firewall rules order

| **Action** | **Interface** | **Protocol** | **Source** | **Source Port** | **Destination** | **Destination Port** | **Description** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **PASS** | [OPNET] | TCP | \* | \* | [LAN] Address | 8443 | Allow access to firewall for management from OPNET |
| **PASS** | [OPNET] | ICMP | \* | \* | [LAN] Net | \* | Allow ICMP from OPNET to LAN for diagnostics |

Table 11 – PROTECT – OPNsense: OPNET Firewall rules order

### 7.2.7 - DMZ Port Forwarding

Next you will configure DMZ port forwarding rules. The Proxmox instructions do not address the DMZ configuration (as of the drafting of this document). This port forwarding rules section is meant for environments that may have Elastic agents deployed in another network. The rules will allow the agent traffic to reach the Elastic Fleet server on the DETECT node.

1. Navigate to Firewall -\> NAT -\> Port Forward. On the Firewall: NAT: Port Forward page, click on the "+" near the upper right corner of the right window pane to add a new entry.
2. On the configuration page, ensure the DMZ interface is selected. TCP/IP version is 4, with TCP being the protocol. For the "from:" source port, choose "other" and enter 9200. Select the DMZ interface address as the destination. For the "from:" destination port, use the same settings as the source. For the "Redirect target IP," select "Single host or Network" and enter the IP address that your DETECT node will use. For the "Redirect target port", use the same settings as the source/destination ports. Enter "Elastic traffic from external agents" for the description. Finally, for "Filter rule association," ensure "Add associated filter rule" is selected. This will create a companion firewall rule on the DMZ interface. 

![](RackMultipart20231221-1-5l8g4e_html_b43cb12dc31b0ab3.png)

---
<div align="center">
| [Previous Section: 6 - Building the vSphere Nodes](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/6%20-%20Building%20the%20vSphere%20Nodes.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 8 - Kali Purple] |
</div>