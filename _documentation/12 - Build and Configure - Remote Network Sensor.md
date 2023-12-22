# 12 - Build and Configure: Remote Network Sensor

## 12.1 - VMware vSphere: VM Configuration

| CPU Cores | Memory | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 2 | 16 GB | Remote Network Sensor | 1 x 100 GB | 2 | MGMT: 192.168.30.x/24
 SPAN: \<promiscuous\> |

*Table 12 - Remote Sensor Hardware*

<details>
<summary><h2>12.2 - Hedgehog Linux Installation</h2></summary>

1. **In vSphere, verify that the Hedgehog Linux ISO is mounted to the desired virtual machine's CD/DVD drive, and configured to connect at Power-on. Right click on the VM, select Power -\> Power On. You'll see a green arrow on the VM icon to show that it's running.**

2. **Take note of the MAC address of the interface to be used for network monitoring. Use the information from Section 7.1 (OPNsense Installation), step 2 for guidance on how to do this.**

3. **Click the VM's icon. On the next page, click the miniature console window to expand the console of the VM. Clicking within the window that appears will change focus to the VM.**

4. **At the initial Hedgehog Linux splash screen, select the "Install Hedgehog Linux" option.**

![](_images/147a._Boot.png)

5. **On the next screen, choose "Virtual Machine Single Partition Quick Install."**

![](_images/147b._Boot.png)

6. **The Hedgehog Linux setup is designed to ask very few questions to the user. The first input screen will be for the root password. Enter a secure password, and then confirm it on the next screen.**

![](_images/147c._Root_Password.png)

7. **The following input screen will ask for a new user password. This will be the password for the service account that will run the sensor services. Enter a secure password, and then confirm it on the next screen.**

![](_images/147e._New_User_Password.png)

8. **After a few minutes of installation, you will be prompted with a few questions (suggested answers in brackets):**

    **(a)** Disable IPv6? **[Yes]**

    **(b)** Automatically login to the GUI session? **[Yes]**

    **(c)** Should the GUI session be locked due to inactivity **[No]**

    **(d)** Display the Standard Mandatory DoD Notice and Consent Banner? **[No]**

    **(e)** Allow SSH password authentication? **[Yes]**
</details>

<details>
<summary><h2>12.3 - Sensor Configuration</h2></summary>

### 12.3.1 - CONFIGURE: Hostname and Interface

1. **The installation will complete with a reboot of the sensor. When it starts up, it will bring you to Hedgehog Linux's kiosk mode. This can be exited with Alt+F4, but to do this without closing your browser, you'll need to be in full screen mode (typically F11 will accomplish this). Once you enter full screen mode, ensure your focus is in the VM by clicking anywhere on the VM screen, then Alt+F4 will close the kiosk, and you'll see the Debian desktop.**

![](_images/148._Hedgehog_Linux_-_Kiosk.png)

*Figure 148 - SENSOR: Kiosk Mode*

![](_images/149._Hedgehog_Linux_-_Desktop.png)

*Figure 149 - SENSOR: Desktop*

2. **On the taskbar at the top of the screen click the icon labelled "Configure Interfaces and Hostname". Enter the root password if prompted. This will open the sensor network interface controller utility. Select "Continue."**

![](_images/149a._Hedgehog_Linux_-_Network_Configuration.png)
![](_images/149b._Network_Interfaces_and_Hostname_Configuration_Utility.png)

3. **On the main screen of the controller utility, choose "Interface."**

![](_images/149d1._Network_Interfaces_and_Hostname_Configuration_Utility.png)

4. **Choose "No" when asked if you need help identifying network interfaces.**

![](_images/149c._Network_Interfaces_and_Hostname_Configuration_Utility.png)

5. **Choose the interface whose MAC address DOES NOT MATCH the one you noted in Section 12.2 (Hedgehog Linux Installation), step 2. Most likely it will be ens192.**

![](_images/149d._Network_Interfaces_and_Hostname_Configuration_Utility.png)

6. **Choose "static" for IP address assignment.**

![](_images/149e._Network_Interfaces_and_Hostname_Configuration_Utility.png)

7. **Configure the sensor with an IP address within the range you've designated for sensors (see Section 5.2). In my example, I dedicated addresses between 192.168.30.20-29. Click OK.**

![](_images/149f._Network_Interfaces_and_Hostname_Configuration_Utility.png)

8. **The sensor will toggle the network interface and then return you to the sensor network interface controller utility welcome screen. Select "Continue," then choose "Hostname."**

9. **You will be provided information about the sensor's current configuration. Select OK. On the next screen, assign a hostname. Select OK. You'll be shown the new information. Select OK, and you'll be returned to the welcome screen.**

![](_images/149g._Network_Interfaces_and_Hostname_Configuration_Utility.png)

10. **Select "Time Synchronization" on the welcome screen. Choose "ntp" for time synchronization.**

![](_images/149h._NTP_Sync.PNG)

11. **Enter the FQDN or IP address of your PROTECT (firewall) node.**

![](_images/149i._NTP_Sync.PNG)

12. **You should get a message informing you that the time synchronization was successful. Select OK, and Quit to exit the utility.**

![](_images/149j._NTP_Sync.PNG)


### 12.3.2 - Filebeat Certificate Transfer

1. **Launch a terminal session using the Tillix shortcut on the taskbar in the upper left.**

![](_images/149k._Hedgehog_Linux_-_Tillix.png)

2. **In the terminal session, issue the following command to switch to the root account:**

        su

**Enter the root password. You'll be dropped into the /opt/sensor/sensor_ctl/ directory.**

3. **In the terminal session, issue the following commands to copy filebeat certificates from your RESPOND node to the sensor:**

        cd filebeat
        scp <RESPOND node admin username>@<RESPOND node FQDN or IP>:/home/<admin username>/Malcolm/filebeat/certs/* .

 **NOTE: There is a space between the "/*" and the "."**

 **This should copy 3 files to the sensor:**

 ca.crt

 client.crt

 client.key

 ![](_images/150._File_Transfer.png)

*Figure 150 - SENSOR: Filebeat Certificate Transfer*

### 12.3.3 - CONFIGURE: Capture and Forwarding

1. **On the taskbar at the top of the screen click the icon labelled "Configure Capture and Forwarding". Enter the root password if prompted. This will open the capture and forwarding configuration utility welcome screen. Select "Continue."**

![](_images/150a1._Capture_and_Forwarding_Config.png)
![](_images/150a._Capture_and_Forwarding_Config.png)

2. **On the main menu, select "Configure Capture" and then OK.**

    **(a) Choose "No" when asked if you need help identifying network interfaces.**

    ![](_images/149c._Network_Interfaces_and_Hostname_Configuration_Utility.png)

    **(b) Choose the interface whose MAC address MATCHES the one you noted in Section 12.2 (Hedgehog Linux Installation), step 2. Most likely it will be ens224. Select OK.**

    ![](_images/150c._Capture_and_Forwarding_Config.png)

    **(c) Leave the PCAP filter blank.**

    **(d) Accept default values for log paths for PCAP and Zeek.**

    **(e) Select "No" on the question about OT/ICS networks.**

    **(f) Select "interesting" for Zeek file carving mode.**

    **(g) Select all scanners for Zeek-carved files.**

    ![](_images/150d._Capture_and_Forwarding_Config.png)

    **(h) Select "quarantined" for file preservation action.**

    **(i) You'll be presented with all the configuration settings you chose. Select OK.**

    **(j) You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.**


3. **Select "Configure Forwarding", then "arkime-capture."**

    **(a) Select HTTPS for Opensearch/Elasticsearch connection protocol.**

    **(b) Select "None" for Opensearch/Elasticsearch SSL verification.**

    **(c) Enter the FQDN or IP address of your RESPOND node for the Opensearch/Elasticsearch host.**

    **(d) Enter the username of the sensor account you created in Section 11.3.6.**

    **(e) Enter the password for the sensor account. Select OK, and the sensor will attempt to authenticate with the RESPOND node.**

    **(f) Leave the default value for the Arkime password hash and confirmation screens.**

    **(g) The PCAP retrieval ACL screen should have only the IP address of your RESPOND node listed. Select OK.**

    **(h) Select "none" for PCAP compression mode.**

    **(i) You'll be presented with all the configuration settings you chose. Select OK.**

    **(j) You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.**

1. **Select "Configure Forwarding", then "filebeat."**

    **(a) Accept the default path for Filebeat log path.**

    **(b) Enter the FQDN or IP address of your RESPOND node for the Logstash host.**

    **(c) Select SSL for Logstash forwarding.**

    **(d) Select "None" for SSL verification.**

    **(e) On the next page, modify each file path by replacing "*logstash-client-certificates*" with "*filebeat*".**

    ![](_images/150e._Capture_and_Forwarding_Config.png)

    **(f) You'll be presented with all the configuration settings you chose. Select OK.**

    **(g) You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.**

2. **Select "Configure Autostart Services."**

    **(a) Select ALL services except:**

    [ ] AUTOSTART_NETSNIFF

    [ ] AUTOSTART_TCPDUMP

    ![](_images/150f._Capture_and_Forwarding_Config.png)

    **NOTE : NETSNIFF and TCPDUMP cannot be run at the same time as ARKIME, as all are packet capture applications.**

    **(b) You'll be presented with all the configuration settings you chose. Select OK.**

    **(c) You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen.**

### 12.3.4 - Reboot Sensor

1. **In a terminal session, issue the following command to reboot the sensor:**

        sudo reboot
        
2. **When the sensor has rebooted, it will re-launch into kiosk mode.**

### 12.3.5 - Verify Sensor Connectivity

In this section, I'll show you how to verify your sensor's connectivity to the RESPOND node (now effectively your Malcolm Network Traffic Analysis Aggregator).

1. **Ensure the sensor has been booted up. It should be in kiosk mode.**

2. **From any node, open a browser and navigate to the FQDN of your RESPOND node (e.g. https://kali-eminence.kali.purple). Enter your Malcolm credentials if prompted. This should bring you to the Arkime sessions page. Click on the "Stats" tab.**

![](_images/150g._Arkime_Sessions.PNG)

3. **On the Stats page, you should see your RESPOND node (labelled "arkime") as well as your sensor(s) listed just below.**

![](_images/150h._Arkime_Stats.PNG)
</details>

---
<div align="center">
| [Previous Section: 11 - Configure: RESPOND Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/11%20-%20Configure%20RESPOND%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 13 - Build and Configure: SOC Analyst Workstation](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/13%20-%20Build%20and%20Configure%20-%20SOC%20Analyst%20Workstation.md) |
</div>