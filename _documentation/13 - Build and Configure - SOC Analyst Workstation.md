# 13 - Build and Configure: SOC Analyst Workstations

## 13.1 - VMware vSphere: VM Configuration

| CPU Cores | Memory | Function | Disks | Network Interfaces | Networks |
| --- | --- | --- | --- | --- | --- |
| 2 | 16 GB | SOC Analyst Workstation | 1 x 128 GB | 1 | OPNET: 192.168.32.x/24 |

*Table 13 - SOC Analyst Workstation Hardware*

![](_images/151._VMware_-_SOC_Analyst.PNG)

*Figure 151 - SOC Analyst Workstation VM Configuration*

## 13.2 - Kali Purple Installation and Configuration

1. **To install Kali Purple, use section 8.1 (Kali Purple) as your installation guide with the following caveats:**

1.1 - **For step 8.1.5 (Disk Settings), do Step 1 only.**

1.2 - **For step 8.1.6 (Software Selection), do Step 1 only.**

2. **After Kali Purple has been installed, log in and update the distribution:**

2.1 - **Update Kali Purple following section 8.2.3 (Updating Kali Purple) as a guide.**

2.2 - **Reboot the system following section 8.2.5 (Reboot Kali Purple).**

 ![](_images/152._SOC_Analyst_-_LVM_Encrypt.PNG)

*Figure 152 – ANALYST - LVM Encrypted*

![](_images/153._SOC_Analyst_-_LVM_Decrypt.PNG)

*Figure 153 – ANALYST - LVM Decrypted*

![](_images/154._SOC_Analyst_-_Login_Screen.PNG)

*Figure 154 - ANALYST - GNOME Login*

---
<div align="center">
| [Previous Section: 12 - Build and Configure: Remote Network Sensor](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/12%20-%20Build%20and%20Configure%20-%20Remote%20Network%20Sensor.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) |
</div>