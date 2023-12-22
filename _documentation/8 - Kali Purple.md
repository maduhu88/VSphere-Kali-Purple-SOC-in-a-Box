# Kali Purple

<details>
<summary><h2>8.1 - Kali Purple Installation</h2></summary>

This section will cover the installation of Kali Purple Linux. These steps are to be used for the DETECT, IDENTIFY, and RESPOND nodes as well as any SOC Analyst workstations you may desire to set up. I will identify any node-specific configurations that may be required, but for the most part, all other steps will be the same. The screenshots used here will be from the DETECT build.

**NOTE** : Ensure that the PROTECT node is powered on and connected to the Internet before building the Kali Purple VMs to allow access for updates.

### 8.1.1 - Initial Settings

1. **In vSphere, verify that the Kali Purple ISO is mounted to the desired virtual machine's CD/DVD drive, and configured to connect at Power-on. Right click on the VM, select Power -\> Power On. You'll see a green arrow on the VM icon to show that it's running.** 

![](RackMultipart20231221-1-5l8g4e_html_af74b6f7eb10bae6.png)

*Figure 56 – VMware vSphere: Power on Kali VM*

2. **Click the VM's icon. On the next page, click the miniature console window to expand the console of the VM. Clicking within the window that appears will change focus to the VM. Choose "Graphical install."**

![](RackMultipart20231221-1-5l8g4e_html_e9e273daa665e5ac.png)

*Figure 57 – Kali Purple Installation Options*

3. **Accept the default settings for language, location, and keyboard setup.** 

![](RackMultipart20231221-1-5l8g4e_html_22cd2971d81fc381.png) 
![](RackMultipart20231221-1-5l8g4e_html_3808bc35d1834199.png) 
![](RackMultipart20231221-1-5l8g4e_html_cee579d5d332d4ea.png)

*Figure 58 – Kali Purple: Initial Configuration*

### 8.1.2 - Network Settings

1. **Choose "Configure network manually."** 

![](RackMultipart20231221-1-5l8g4e_html_7010de0649343d08.png)

*Figure 59 – Kali Purple: Manual network configuration*

2. **Next, enter the desired IP address in CIDR notation (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](RackMultipart20231221-1-5l8g4e_html_daaf4272835beb11.png)

*Figure 60 – Kali Purple: IP addressing*

3. **Use the IP address for the LAN interface of the PROTECT node as both the gateway and the name server (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](RackMultipart20231221-1-5l8g4e_html_21b03e2cc00a7e2f.png) 
![](RackMultipart20231221-1-5l8g4e_html_c77c8498a0011960.png)

*Figure 61 – Kali Purple: Gateway and DNS configuration*

4. **Enter the desired name for the node (refer to [Table 6](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/5%20-%20SOC%20Data%20Collection.md#52-network-management)).** 

![](RackMultipart20231221-1-5l8g4e_html_8385e6aad9f70bc5.png)

*Figure 62 – Kali Purple: Hostname*

5. **Enter the desired domain name for the node. As in the Proxmox documentation, I will use "kali.purple" for this deployment.** 

![](RackMultipart20231221-1-5l8g4e_html_52bc7a7c4d9fd1f4.png)

*Figure 63 – Kali Purple: Domain name*

### 8.1.3 - User Settings

1. **Enter the desired display name of your user account. This account will have 'sudo' privileges.** 

![](RackMultipart20231221-1-5l8g4e_html_81dc3897dd39a975.png)

*Figure 64 – Kali Purple: New user display name*

2. **Enter the desired username for the account created in the previous step.** 

![](RackMultipart20231221-1-5l8g4e_html_b21b21f1ffbf2e95.png)

*Figure 65 – Kali Purple: New user username*

3. **Set and confirm a secure password for you new user account.** 

![](RackMultipart20231221-1-5l8g4e_html_8f018d15ef26d27b.png)

*Figure 66 – Kali Purple: New user password*

### 8.1.4 - Time Zone Settings

1. **Select your desired time zone.** 

![](RackMultipart20231221-1-5l8g4e_html_7ce98b3ea346a94.png)

*Figure 67 – Kali Purple: Time zone*

### Disk Settings

**NOTE** : If you are not setting up SOC analyst workstations, skip to Step 2 of this section.

1. **SOC Analyst Workstations:**
    1. **For disk partitioning, select "Guided – use entire disk and set up encrypted LVM."** 

    ![](RackMultipart20231221-1-5l8g4e_html_8d2822f6c848cb68.png)

    2. **Ensure the virtual hard disk is selected.** 

    ![](RackMultipart20231221-1-5l8g4e_html_322caf0dd2300f4a.png)

    3. **Choose "All files in one partition."** 

    ![](RackMultipart20231221-1-5l8g4e_html_d6b935829cae66ce.png)

    4. **Choose "Yes" to confirm LVM configuration.** 

    ![](RackMultipart20231221-1-5l8g4e_html_3711528018c19ad7.png)

    5. **Enter and confirm a passphrase for your encrypted partition. Each time the workstation boots, it will ask for this passphrase to unlock the drive.** 

    ![](RackMultipart20231221-1-5l8g4e_html_f2db5eef79355881.png)

    6. **Enter "max" for the amount of the drive to be used for guided partitioning.** 

    ![](RackMultipart20231221-1-5l8g4e_html_571fe483e290dd16.png)

    7. **Select "Finish partitioning and write changes to disk."** 

    ![](RackMultipart20231221-1-5l8g4e_html_a9b0ae5af0b01257.png)

    8. **Choose "Yes" to confirm changes.**

    ![](RackMultipart20231221-1-5l8g4e_html_93cccee438472aa7.png)

    9. **If desired, include the "GNOME" desktop environment as well as the role-specific tools. In this example, I am including the IDENTIFY toolset.**

    ![](RackMultipart20231221-1-5l8g4e_html_6b42dbe6c20930e.png)
    
2. **SOC Nodes:**
    1. **For disk partitioning, select "Manual."**

    ![](RackMultipart20231221-1-5l8g4e_html_b4e3b233f088bd4e.png)

    *Figure 68 - Kali Purple: Disk: Manual partitioning*

    2. **Double-click the hard disk.**

    ![](RackMultipart20231221-1-5l8g4e_html_de87d825ecf8b2c1.png)

    3. **Select "Yes" to create a new empty partition table on the disk.** 

    ![](RackMultipart20231221-1-5l8g4e_html_dd396a5dc33f1dd1.png)

    4. **Double-click the entry containing "FREE SPACE."** 

    ![](RackMultipart20231221-1-5l8g4e_html_ad861456a595a82e.png)

    5. **Choose "Create a new partition." This will be the swap partition.** 

    ![](RackMultipart20231221-1-5l8g4e_html_6892ad1aad19c467.png)

    6. **Enter 24 GB for the size.** 

    ![](RackMultipart20231221-1-5l8g4e_html_75466c3b1d246b34.png)

    7. **Choose "Logical" for the partition type.** 

    ![](RackMultipart20231221-1-5l8g4e_html_e1efc572e58306e0.png)

    8. **Choose "End" to have this partition created on the tail end of the available space.**

    ![](RackMultipart20231221-1-5l8g4e_html_30b9705e471ee53.png)

    9. **Double-click the "Use as:" entry to change the purpose of the partition.**

    ![](RackMultipart20231221-1-5l8g4e_html_389b43eb7acb1909.png)

    10. **Select "swap area."**

    ![](RackMultipart20231221-1-5l8g4e_html_a0644958f2522208.png)

    11. **Select "Done setting up the partition."**

    ![](RackMultipart20231221-1-5l8g4e_html_a74e7cba353b3434.png)

    12. **Next, double-click on the entry containing "FREE SPACE."** 

    ![](RackMultipart20231221-1-5l8g4e_html_74c1cedb9ba74add.png)

    13. **Choose "Create a new partition."** 

    ![](RackMultipart20231221-1-5l8g4e_html_c527ddb75940958a.png)

    14. **Enter "max" for the size.** 

    ![](RackMultipart20231221-1-5l8g4e_html_16b797db91e425f7.png)

    15. **Choose "Primary" for the partition type.** 

    ![](RackMultipart20231221-1-5l8g4e_html_4321f131cace278f.png)

    16. **Select "Done setting up the partition."** 

    ![](RackMultipart20231221-1-5l8g4e_html_e2a09e8e1411db7.png)

    17. **Select "Finish partitioning and write changes to disk."** 

    ![](RackMultipart20231221-1-5l8g4e_html_fb146b000b69d8a9.png)

    18. **Select "Yes" to confirm your selection.**

    ![](RackMultipart20231221-1-5l8g4e_html_f74202c18afa5abf.png)

    *Figure 69 - Kali Purple: Disk Partition Layout*

### 8.1.6 - Software Selection

**NOTE** : If you are not setting up SOC analyst workstations, skip to Step 2 of this section.

1. **SOC Analyst Workstations** :
    1. **If desired, include the "GNOME" desktop environment as well as the role-specific tools. In this example, I am including the IDENTIFY toolset.**

    ![](RackMultipart20231221-1-5l8g4e_html_6b42dbe6c20930e.png)

    2. **Accept the default display manager (gdm3).** 

    ![](RackMultipart20231221-1-5l8g4e_html_ee4929fecd7a3630.png)

2. **SOC Nodes** : **At this point, the Kali Purple setup will begin installing the OS. When the "Software selection screen appears, use the following settings (ALL SOC nodes use the same settings):** 

![](RackMultipart20231221-1-5l8g4e_html_fd82d7999892993.png)

*Figure 70 - Kali Purple: Software selection*

### 8.1.7 - Finishing Installation

1. **When the "Install the GRUB boot loader" screen appears, choose "Yes."** 

![](RackMultipart20231221-1-5l8g4e_html_64160c72681ce71a.png)

*Figure 71 - Kali Purple: GRUB configuration*

1. **Select "/dev/sda."** 

![](RackMultipart20231221-1-5l8g4e_html_90d248ba4d6de5a6.png)

2. **When the "Finish the installation" screen appears, remove the ISO from the VM by selecting "Host device" under the VM's CD/DVD Drive settings in vSphere.** 

![](RackMultipart20231221-1-5l8g4e_html_a1a4b33bea777caf.png)

*Figure 72 - Kali Purple: ISO *removal*

1. **Back on the VMs console, click continue to reboot the VM.** 

![](RackMultipart20231221-1-5l8g4e_html_88a3d9a544c64b15.png)

*Figure 73 - Kali Purple: Finished Installation*
</details>

<details>
<summary><h2>8.2 - Kali Purple Configuration</h2></summary>

### 8.2.1 - Installing xRDP

1. **Log into your Kali Purple VM using the user credentials you created during setup.** 

![](RackMultipart20231221-1-5l8g4e_html_7ddca3ee0a12c1c3.png)

*Figure 74 - Kali Purple: Login Screen*

2. **Launch a terminal session.** 

![](RackMultipart20231221-1-5l8g4e_html_e9347639f7bfbaa4.png)

*Figure 75 - Kali Purple: Launch Terminal*

3. **In your terminal session, enter the following command to install xRDP (enter your password when prompted):** 

        sudo apt install xrdp -y

![](RackMultipart20231221-1-5l8g4e_html_d9db1ddbfbd9ad94.png)

*Figure 76 - Kali Purple: Install xRDP*

4. **Keep this terminal session open for the next step.**

### 8.2.2 - Fixing xRDP login error

**The following commands are to be entered into a terminal session.**

1. **Enter "_sudo wget -P /etc/polkit-1/localauthority /50-local.d  https://gitlab.com/kalilinux/documentation/kali-purple/-/raw/main/301\_kali-purple/overlays/etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla_" to fix a known login error with xRDP. Enter your password if prompted.** 

![](RackMultipart20231221-1-5l8g4e_html_ca215d5ffac59e43.png)

*Figure 77 - Kali Purple: Fix xRDP Login Error*

2. **Keep this terminal session open for the next step.**

### 8.2.3 - Updating Kali Purple

**The following commands are to be entered into a terminal session.**

1. **Enter "_sudo update-grub_" to update the GRUB bootloader. Enter your password if prompted.**

![](RackMultipart20231221-1-5l8g4e_html_a1ec261e98d52386.png)

*Figure 78 - Kali Purple: Update GRUB bootloader*

1. **Enter "_sudo apt update_" to fetch the latest version of the package lists for the Kali Purple distribution.** 

![](RackMultipart20231221-1-5l8g4e_html_a7a4028cf682ae7b.png)

*Figure 79 - Kali Purple: APT update*

1. **Enter "_sudo apt full-upgrade -y_" to install/update any packages that requires upgrading. This will take several minutes to complete.**

![](RackMultipart20231221-1-5l8g4e_html_b737e8cdef93ef54.png)

*Figure 80 - Kali Purple: Upgrading Kali Purple*

1. **One of the updates you may encounter will ask about configuring a character set for the console-setup. Press "ENTER" to accept defaults.** 

![](RackMultipart20231221-1-5l8g4e_html_7b6275f8f59c942d.png)

2. **Keep this terminal session open for the next step.**

### 8.2.4 - Enabling xRDP and SSH

**In a terminal session, enter the following command to set the xRDP and SSH services to auto-start (enter your password if prompted):**

    sudo systemctl enable xrdp ssh --now 

![](RackMultipart20231221-1-5l8g4e_html_149a3f88ab877c82.png)

*Figure 81 - Kali Purple: Setting SSH/xRDP to auto-start*

### 8.2.5 - Set up NTP synchronization

**In a terminal session, issue the following commands to synchronize time with the PROTECT node:**

    sudo timedatectl set-ntp true

    sudo sed -e 's/#NTP=/NTP=\<PROTECT FQDN or IP\>/g' -i /etc/system/timesync.conf
 
![](RackMultipart20231221-1-5l8g4e_html_be70ee8381d23abe.png)

### 8.2.6 - Reboot Kali Purple

**In a terminal session, issue the command to reboot the VM:**

    sudo reboot

</details>  

---
<div align="center">
| [Previous Section: 7 - Configure: PROTECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/7%20-%20Configuring%20the%20PROTECT%20node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 9 - Configure: DETECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/9%20-%20Configure%20DETECT%20Node.md) |
</div>