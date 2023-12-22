# 12 - Build and Configure: Remote Network Sensor

## 12.1 - VMware vSphere: VM Configuration

| CPU Cores | Memory | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 2 | 16 GB | Remote Network Sensor | 1 x 100 GB | 2 | MGMT: 192.168.30.x/24
 SPAN: \<promiscuous\> |
|
| |

*Table 12 - Remote Sensor Hardware*

## 12.2 - Hedgehog Linux Installation

      1. In vSphere, verify that the Hedgehog Linux ISO is mounted to the desired virtual machine's CD/DVD drive, and configured to connect at Power-on. Right click on the VM, select Power -\> Power On. You'll see a green arrow on the VM icon to show that it's running.
      2. Take note of the MAC address of the interface to be used for network monitoring. Use the information from Section 7.1 (OPNsense Installation), step 2 for guidance on how to do this.
      3. Click the VM's icon. On the next page, click the miniature console window to expand the console of the VM. Clicking within the window that appears will change focus to the VM.
      4. At the initial Hedgehog Linux splash screen, select the "Install Hedgehog Linux" option.
 ![](RackMultipart20231221-1-5l8g4e_html_75494683ffeda150.png)
      5. On the next screen, choose "Virtual Machine Single Partition Quick Install."
 ![](RackMultipart20231221-1-5l8g4e_html_b61b40b6cfd1133b.png)
      6. The Hedgehog Linux setup is designed to ask very few questions to the user. The first input screen will be for the root password. Enter a secure password, and then confirm it on the next screen.
 ![](RackMultipart20231221-1-5l8g4e_html_45130c31dae57f15.png)
      7. The following input screen will ask for a new user password. This will be the password for the service account that will run the sensor services. Enter a secure password, and then confirm it on the next screen.
 ![](RackMultipart20231221-1-5l8g4e_html_178260885e64f204.png)
      8. After a few minutes of installation, you will be prompted with a few questions (suggested answers in brackets):
        1. Disable IPv6? [Yes]
        2. Automatically login to the GUI session? [Yes]
        3. Should the GUI session be locked due to inactivity [No]
        4. Display the Standard Mandatory DoD Notice and Consent Banner? [No]
      9. Allow SSH password authentication? [Yes]
## 12.3 - Sensor Configuration

### 12.3.1 - CONFIGURE: Hostname and Interface

      1. The installation will complete with a reboot of the sensor. When it starts up, it will bring you to Hedgehog Linux's kiosk mode. This can be exited with Alt+F4, but to do this without closing your browser, you'll need to be in full screen mode (typically F11 will accomplish this). Once you enter full screen mode, ensure your focus is in the VM by clicking anywhere on the VM screen, then Alt+F4 will close the kiosk, and you'll see the Debian desktop.
 ![](RackMultipart20231221-1-5l8g4e_html_e22cdecb50d99c23.png)

*Figure 148 - SENSOR: Kiosk Mode*

      1.
 ![](RackMultipart20231221-1-5l8g4e_html_c338f2d5ef5b67d6.png)

*Figure 149 - SENSOR: Desktop*

      1. On the taskbar at the top of the screen click the icon labelled "Configure Interfaces and Hostname". Enter the root password if prompted. This will open the sensor network interface controller utility. Select "Continue."
 ![](RackMultipart20231221-1-5l8g4e_html_3b4162c499f7548b.png)
 ![](RackMultipart20231221-1-5l8g4e_html_6d573add25774201.png)
      2. On the main screen of the controller utility, choose "Interface."
 ![](RackMultipart20231221-1-5l8g4e_html_d142df69e37dce9c.png)
      3. Choose "No" when asked if you need help identifying network interfaces.
 ![](RackMultipart20231221-1-5l8g4e_html_b7efe2aa07769839.png)
      4. Choose the interface whose MAC address DOES NOT MATCH the one you noted in Section 12.2 (Hedgehog Linux Installation), step 2. Most likely it will be ens192.
 ![](RackMultipart20231221-1-5l8g4e_html_72199dcf090f245e.png)
      5. Choose "static" for IP address assignment.
 ![](RackMultipart20231221-1-5l8g4e_html_52e98a4144bdeb81.png)
      6. Configure the sensor with an IP address within the range you've designated for sensors (see Section 5.2). In my example, I dedicated addresses between 192.168.30.20-29. Click OK.
 ![](RackMultipart20231221-1-5l8g4e_html_258399ba0bbe01aa.png)
      7. The sensor will toggle the network interface and then return you to the sensor network interface controller utility welcome screen. Select "Continue," then choose "Hostname."


      8. You will be provided information about the sensor's current configuration. Select OK. On the next screen, assign a hostname. Select OK. You'll be shown the new information. Select OK, and you'll be returned to the welcome screen.
 ![](RackMultipart20231221-1-5l8g4e_html_770644eba957067f.png)
      9. Select "Time Synchronization" on the welcome screen. Choose "ntp" for time synchronization.
 ![](RackMultipart20231221-1-5l8g4e_html_9c2a2238c8d6cbf9.png)
      10. Enter the FQDN or IP address of your PROTECT (firewall) node.
 ![](RackMultipart20231221-1-5l8g4e_html_335578df68df5320.png)
      11. You should get a message informing you that the time synchronization was successful. Select OK, and Quit to exit the utility.
 ![](RackMultipart20231221-1-5l8g4e_html_f497a567e21f629f.png)

### 12.3.2 - Filebeat Certificate Transfer

      1. Launch a terminal session using the Tillix shortcut on the taskbar in the upper left.
 ![](RackMultipart20231221-1-5l8g4e_html_8a6b6015a12ae3d3.png)
      2. In the terminal session, issue the following command to switch to the root account:
**su**
 Enter the root password. You'll be dropped into the /opt/sensor/sensor\_ctl/ directory.
      3. In the terminal session, issue the following commands to copy filebeat certificates from your RESPOND node to the sensor:
**cd filebeat
 scp \< RESPOND node admin username\>@\<RESPOND node FQDN or IP\>:/home/\<admin username\>/Malcolm/filebeat/certs/\* .**

 NOTE: There is a space between the " **/\***" and the " **.**".

 This should copy 3 files to the sensor:
 ca.crt
 client.crt
 client.key
 ![](RackMultipart20231221-1-5l8g4e_html_8d7392d4dff256fd.png)

*Figure 150 - SENSOR: Filebeat Certificate Transfer*

### 12.3.3 - CONFIGURE: Capture and Forwarding

      1. On the taskbar at the top of the screen click the icon labelled "Configure Capture and Forwarding". Enter the root password if prompted. This will open the capture and forwarding configuration utility welcome screen. Select "Continue."
 ![](RackMultipart20231221-1-5l8g4e_html_178a897a55d740db.png)
 ![](RackMultipart20231221-1-5l8g4e_html_7dd6b34602c285b2.png)
      2. On the main menu, select "Configure Capture" and then OK.

        1. Choose "No" when asked if you need help identifying network interfaces.
 ![](RackMultipart20231221-1-5l8g4e_html_b7efe2aa07769839.png)
        2. Choose the interface whose MAC address MATCHES the one you noted in Section 12.2 (Hedgehog Linux Installation), step 2. Most likely it will be ens224. Select OK.
 ![](RackMultipart20231221-1-5l8g4e_html_353a1f9c445ebaed.png)
        3. Leave the PCAP filter blank.
        4. Accept default values for log paths for PCAP and Zeek.
        5. Select "No" on the question about OT/ICS networks.
        6. Select "interesting" for Zeek file carving mode.
        7. Select all scanners for Zeek-carved files.
 ![](RackMultipart20231221-1-5l8g4e_html_4768d9bcaa678598.png)
        8. Select "quarantined" for file preservation action.
        9. You'll be presented with all the configuration settings you chose. Select OK.
        10. You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.
      3. Select "Configure Forwarding", then "arkime-capture."

          1. Select HTTPS for Opensearch/Elasticsearch connection protocol.
          2. Select "None" for Opensearch/Elasticsearch SSL verification.
          3. Enter the FQDN or IP address of your RESPOND node for the Opensearch/Elasticsearch host.
          4. Enter the username of the sensor account you created in Section 11.3.6.
          5. Enter the password for the sensor account. Select OK, and the sensor will attempt to authenticate with the RESPOND node.
          6. Leave the default value for the Arkime password hash and confirmation screens.
          7. The PCAP retrieval ACL screen should have only the IP address of your RESPOND node listed. Select OK.
          8. Select "none" for PCAP compression mode.
          9. You'll be presented with all the configuration settings you chose. Select OK.
          10. You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.
        1. Select "Configure Forwarding", then "filebeat."
          1. Accept the default path for Filebeat log path.
          2. Enter the FQDN or IP address of your RESPOND node for the Logstash host.
          3. Select SSL for Logstash forwarding.
          4. Select "None" for SSL verification.
          5. On the next page, modify each file path by replacing "logstash-client-certificates" with "filebeat".
 ![](RackMultipart20231221-1-5l8g4e_html_e6a0628a50824128.png)
          6. You'll be presented with all the configuration settings you chose. Select OK.
          7. You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen. Select OK.
        2. Select "Configure Autostart Services."
          1. Select ALL services except:
            1. AUTOSTART\_NETSNIFF
            2. AUTOSTART\_TCPDUMP
 ![](RackMultipart20231221-1-5l8g4e_html_4adee32d79aaabab.png)
**NOTE** : NETSNIFF and TCPDUMP cannot be run at the same time as ARKIME, as all are packet capture applications.
          2. You'll be presented with all the configuration settings you chose. Select OK.
          3. You'll be informed that a reboot will be necessary to commit all changes. Select OK. You'll be returned to the welcome screen.

### 12.3.4 - Reboot Sensor

      1. In a terminal session, issue the command " **sudo reboot**" to reboot the sensor.
      2. When the sensor has rebooted, it will re-launch into kiosk mode.

### 12.3.5 - Verify Sensor Connectivity

In this section, I'll show you how to verify your sensor's connectivity to the RESPOND node (now effectively your Malcolm Network Traffic Analysis Aggregator).

      1. Ensure the sensor has been booted up. It should be in kiosk mode.
      2. From any node, open a browser and navigate to the FQDN of your RESPOND node (e.g. https://kali-eminence.kali.purple). Enter your Malcolm credentials if prompted. This should bring you to the Arkime sessions page. Click on the "Stats" tab.
 ![](RackMultipart20231221-1-5l8g4e_html_ef7473446eae794e.png)
      3. On the Stats page, you should see your RESPOND node (labelled "arkime") as well as your sensor(s) listed just below.
 ![](RackMultipart20231221-1-5l8g4e_html_cd814c90b15f924.png)

---
<div align="center">
| [Previous Section: 11 - Configure: RESPOND Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/11%20-%20Configure%20RESPOND%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 13 - Build and Configure: SOC Analyst Workstation](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/13%20-%20Build%20and%20Configure%20-%20SOC%20Analyst%20Workstation.md) |
</div>