# Kali Purple

<details>
<summary><h2>8.1 - Kali Purple Installation</h2></summary>

This section will cover the installation of Kali Purple Linux. These steps are to be used for the DETECT, IDENTIFY, and RESPOND nodes as well as any SOC Analyst workstations you may desire to set up. I will identify any node-specific configurations that may be required, but for the most part, all other steps will be the same. The screenshots used here will be from the DETECT build.

**NOTE** : Ensure that the PROTECT node is powered on and connected to the Internet before building the Kali Purple VMs to allow access for updates.

### 8.1.1 - Initial Settings

1. **In vSphere, verify that the Kali Purple ISO is mounted to the desired virtual machine's CD/DVD drive, and configured to connect at Power-on. Right click on the VM, select Power -\> Power On. You'll see a green arrow on the VM icon to show that it's running.** 

![](_images/56._DETECT_-_Power_On_VM.PNG)

*Figure 56 – VMware vSphere: Power on Kali VM*

2. **Click the VM's icon. On the next page, click the miniature console window to expand the console of the VM. Clicking within the window that appears will change focus to the VM. Choose "Graphical install."**

![](_images/57._Kali_Splash.PNG)

*Figure 57 – Kali Purple Installation Options*

3. **Accept the default settings for language, location, and keyboard setup.** 

![](_images/58a._-_Kali_Setup_-_Language.PNG) 
![](_images/58b_-_Kali_Setup_-_Location.PNG) 
![](_images/58c._Kali_Setup_-_Keyboard.PNG)

*Figure 58 – Kali Purple: Initial Configuration*

### 8.1.2 - Network Settings

1. **Choose "Configure network manually."** 

![](_images/59._-_Kali_Setup_-_Network_Manual_Setup.PNG)

*Figure 59 – Kali Purple: Manual network configuration*

2. **Next, enter the desired IP address in CIDR notation (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](_images/60._-_Kali_Setup_-_Network_Manual_Setup.PNG)

*Figure 60 – Kali Purple: IP addressing*

3. **Use the IP address for the LAN interface of the PROTECT node as both the gateway and the name server (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](_images/61a.__Kali_Setup_-_Network_Manual_Setup.PNG) 
![](_images/61b.__Kali_Setup_-_Network_Manual_Setup.PNG)

*Figure 61 – Kali Purple: Gateway and DNS configuration*

4. **Enter the desired name for the node (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](_images/62._Kali_Setup_-_Hostname.PNG)

*Figure 62 – Kali Purple: Hostname*

5. **Enter the desired domain name for the node. As in the Proxmox documentation, I will use "kali.purple" for this deployment.** 

![](_images/63._Kali_Setup_-_Domain_name.PNG)

*Figure 63 – Kali Purple: Domain name*

### 8.1.3 - User Settings

1. **Enter the desired display name of your user account. This account will have 'sudo' privileges.** 

![](_images/64.__Kali_Setup_-_New_User.PNG)

*Figure 64 – Kali Purple: New user display name*

2. **Enter the desired username for the account created in the previous step.** 

![](_images/65._Kali_Setup_-_New_User.PNG)

*Figure 65 – Kali Purple: New user username*

3. **Set and confirm a secure password for you new user account.** 

![](_images/66._Kali_Setup_-_New_User.PNG)

*Figure 66 – Kali Purple: New user password*

### 8.1.4 - Time Zone Settings

1. **Select your desired time zone.** 

![](_images/67._Kali_Setup_-_Time_Zone.PNG)

*Figure 67 – Kali Purple: Time zone*

### 8.1.5 - Disk Settings

**NOTE** : If you are not setting up SOC analyst workstations, skip to Step 2 (SOC Nodes) of this section.

#### 8.1.5.1 - SOC Analyst Workstations:

1. **For disk partitioning, select "Guided – use entire disk and set up encrypted LVM."** 

![](_images/1a._Analyst_Disk_Partition.PNG)

2. **Ensure the virtual hard disk is selected.** 

![](_images/1b._Analyst_Disk_Partition.PNG)

3. **Choose "All files in one partition."** 

![](_images/1c._Analyst_Disk_Partition.PNG)

4. **Choose "Yes" to confirm LVM configuration.** 

![](_images/1d._Analyst_Disk_Partition.PNG)

5. **Enter and confirm a passphrase for your encrypted partition. Each time the workstation boots, it will ask for this passphrase to unlock the drive.** 

![](_images/1e._Analyst_Disk_Partition.PNG)

6. **Enter "max" for the amount of the drive to be used for guided partitioning.** 

![](_images/1f._Analyst_Disk_Partition.PNG)

7. **Select "Finish partitioning and write changes to disk."** 

![](_images/1g._Analyst_Disk_Partition.PNG)

8. **Choose "Yes" to confirm changes.**

![](_images/1h._Analyst_Disk_Partition.PNG)

9. **If desired, include the "GNOME" desktop environment as well as the role-specific tools. In this example, I am including the IDENTIFY toolset.**

![](_images/2._Analyst_Software_Selection.PNG)
    
#### 8.1.5.2 - SOC Nodes:
1. **For disk partitioning, select "Manual."**

![](_images/SOC_Nodes_-_Disk_1_-_Kali_Setup_-_Disk_Setup.PNG)

*Figure 68 - Kali Purple: Disk: Manual partitioning*

2. **Double-click the hard disk.**

![](_images/SOC_Nodes_-_Disk_2_-_Kali_Setup_-_Disk_Setup.PNG)

3. **Select "Yes" to create a new empty partition table on the disk.** 

![](_images/SOC_Nodes_-_Disk_3_-_Kali_Setup_-_Disk_Setup.PNG)

4. **Double-click the entry containing "FREE SPACE."** 

![](_images/SOC_Nodes_-_Disk_4_-_Kali_Setup_-_Disk_Setup.PNG)

5. **Choose "Create a new partition." This will be the swap partition.** 

![](_images/SOC_Nodes_-_Disk_5_-_Kali_Setup_-_Disk_Setup.PNG)

6. **Enter 24 GB for the size.** 

![](_images/SOC_Nodes_-_Disk_6_-_Kali_Setup_-_Disk_Setup.PNG)

7. **Choose "Logical" for the partition type.** 

![](_images/SOC_Nodes_-_Disk_7_-_Kali_Setup_-_Disk_Setup.PNG)

8. **Choose "End" to have this partition created on the tail end of the available space.**

![](_images/SOC_Nodes_-_Disk_8_-_Kali_Setup_-_Disk_Setup.PNG)

9. **Double-click the "Use as:" entry to change the purpose of the partition.**

![](_images/SOC_Nodes_-_Disk_9_-_Kali_Setup_-_Disk_Setup.PNG)

10. **Select "swap area."**

![](_images/SOC_Nodes_-_Disk_10_-_Kali_Setup_-_Disk_Setup.PNG)

11. **Select "Done setting up the partition."**

![](_images/SOC_Nodes_-_Disk_11_-_Kali_Setup_-_Disk_Setup.PNG)

12. **Next, double-click on the entry containing "FREE SPACE."** 

![](_images/SOC_Nodes_-_Disk_12_-_Kali_Setup_-_Disk_Setup.PNG)

13. **Choose "Create a new partition."** 

![](_images/SOC_Nodes_-_Disk_13_-_Kali_Setup_-_Disk_Setup.PNG)

14. **Enter "max" for the size.** 

![](_images/SOC_Nodes_-_Disk_14_-_Kali_Setup_-_Disk_Setup.PNG)

15. **Choose "Primary" for the partition type.** 

![](_images/SOC_Nodes_-_Disk_15_-_Kali_Setup_-_Disk_Setup.PNG)

16. **Select "Done setting up the partition."** 

![](_images/SOC_Nodes_-_Disk_16_-_Kali_Setup_-_Disk_Setup.PNG)

17. **Select "Finish partitioning and write changes to disk."** 

![](_images/SOC_Nodes_-_Disk_17_-_Kali_Setup_-_Disk_Setup.PNG)

18. **Select "Yes" to confirm your selection.**

![](_images/SOC_Nodes_-_Disk_18_-_Kali_Setup_-_Disk_Setup.PNG)

*Figure 69 - Kali Purple: Disk Partition Layout*

### 8.1.6 - Software Selection

**NOTE** : If you are not setting up SOC analyst workstations, skip to Step 2 of this section.

1. **SOC Analyst Workstations** :
    1. **If desired, include the "GNOME" desktop environment as well as the role-specific tools. In this example, I am including the IDENTIFY toolset.**

    ![](_images/2._Analyst_Software_Selection.PNG)

    2. **Accept the default display manager (gdm3).** 

    ![](_images/3._Analyst_Display_Manager.PNG)

2. **SOC Nodes** : **At this point, the Kali Purple setup will begin installing the OS. When the "Software selection screen appears, use the following settings (ALL SOC nodes use the same settings):** 

![](_images/70._Kali_Setup_-_Software_Selection.PNG)

*Figure 70 - Kali Purple: Software selection*

### 8.1.7 - Finishing Installation

1. **When the "Install the GRUB boot loader" screen appears, choose "Yes."** 

![](_images/71a._Kali_Setup_-_GRUB.PNG)

*Figure 71 - Kali Purple: GRUB configuration*

1. **Select "/dev/sda."** 

![](_images/71b._Kali_Setup_-_GRUB.PNG)

2. **When the "Finish the installation" screen appears, remove the ISO from the VM by selecting "Host device" under the VM's CD/DVD Drive settings in vSphere.** 

![](_images/72._Kali_Purple_-_Remove_ISO.PNG)

*Figure 72 - Kali Purple: ISO removal*

1. **Back on the VMs console, click continue to reboot the VM.** 

![](_images/73._Kali_Setup_-_Reboot.PNG)

*Figure 73 - Kali Purple: Finished Installation*
</details>

<details>
<summary><h2>8.2 - Kali Purple Configuration</h2></summary>

### 8.2.1 - Installing xRDP

1. **Log into your Kali Purple VM using the user credentials you created during setup.** 

![](_images/74._Kali_Purple_Login.PNG)

*Figure 74 - Kali Purple: Login Screen*

2. **Launch a terminal session.** 

![](_images/75._Terminal.PNG)

*Figure 75 - Kali Purple: Launch Terminal*

3. **In your terminal session, enter the following command to install xRDP (enter your password when prompted):** 

        sudo apt install xrdp -y

![](_images/76._Install_xRDP.PNG)

*Figure 76 - Kali Purple: Install xRDP*

4. **Keep this terminal session open for the next step.**

### 8.2.2 - Fixing xRDP login error

**The following commands are to be entered into a terminal session.**

1. **Enter the following command to fix a known login error with xRDP. Enter your password if prompted.**

        sudo wget -P /etc/polkit-1/localauthority/50-local.d https://gitlab.com/kalilinux/documentation/kali-purple/-/raw/main/301\_kali-purple/overlays/etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla

![](_images/77._Fix_xRDP.PNG)

*Figure 77 - Kali Purple: Fix xRDP Login Error*

2. **Keep this terminal session open for the next step.**

### 8.2.3 - Updating Kali Purple

**The following commands are to be entered into a terminal session.**

1. **Enter the following command to update the GRUB bootloader. Enter your password if prompted.**

        sudo update-grub

![](_images/78._Update-grub.PNG)

*Figure 78 - Kali Purple: Update GRUB bootloader*

1. **Enter the following command to fetch the latest version of the package lists for the Kali Purple distribution:**

        sudo apt update

![](_images/79._APT_update.PNG)

*Figure 79 - Kali Purple: APT update*

1. **Enter the following command to install/update any packages that requires upgrading. This will take several minutes to complete.**

        sudo apt full-upgrade -y

![](_images/80._APT_Full_Upgrade.PNG)

*Figure 80 - Kali Purple: Upgrading Kali Purple*

1. **One of the updates you may encounter will ask about configuring a character set for the console-setup. Press "ENTER" to accept defaults.** 

![](_images/80.1_Update_-_Console_Setup.PNG)

2. **Keep this terminal session open for the next step.**

### 8.2.4 - Enabling xRDP and SSH

**In a terminal session, enter the following command to set the xRDP and SSH services to auto-start (enter your password if prompted):**

    sudo systemctl enable xrdp ssh --now 

![](_images/81._Enable_xRDP_and_SSH.PNG)

*Figure 81 - Kali Purple: Setting SSH/xRDP to auto-start*

### 8.2.5 - Set up NTP synchronization

**In a terminal session, issue the following commands to synchronize time with the PROTECT node:**

    sudo timedatectl set-ntp true

    sudo sed -e 's/#NTP=/NTP=\<PROTECT FQDN or IP\>/g' -i /etc/system/timesync.conf
 
![](_images/81.1_Kali_-_NTP_Sync.PNG)

### 8.2.6 - Reboot Kali Purple

**In a terminal session, issue the command to reboot the VM:**

    sudo reboot

</details>  

---
<div align="center">
| [Previous Section: 7 - Configure: PROTECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/7%20-%20Configuring%20the%20PROTECT%20node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 9 - Configure: DETECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/9%20-%20Configure%20DETECT%20Node.md) |
</div>