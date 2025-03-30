# 11 - Configure: RESPOND Node

**Mar 29, 2025: I am currently working on an updated instruction set with newer software versions.**

## 11.1 - Elastic Agent

**To install the Elastic Agent on this host, follow the instructions in [Section 10.1.1 (Elastic Agent)].**

<details>
<summary><h2>11.2 - Docker and Malcolm Prerequisites</h2></summary>

### 11.2.1 - Installation

1. **In a terminal session, issue the following command to install Docker and prerequisite packages for Malcolm:**

        sudo apt install nginx apache2-utils python3-pip python3-requests python3-dialog apt-transport-https gnupg-agent software-properties-common docker.io docker-compose

![](_images/135._Install_Docker_and_Malcolm_Prereqs.PNG)

*Figure 135 - RESPOND: Docker install with Malcolm Prerequisites*

1. **In the terminal session, issue the following command to set the Docker service to automatically start:**

        sudo systemctl enable docker --now

### 11.2.2 - Portainer

**To install the Portainer on this host, follow the instructions in section 10.2.2 (Portainer). Make sure to replace any references for your IDENTIFY node with your RESPOND node (name/IP address).**
</details>

<details>
<summary><h2>11.3 - INL Malcolm</h2></summary>

### 11.3.1 - Clone Malcolm Repository

**In a terminal session, issue the following commands to clone the INL Malcolm repository (version 23.10.0) to your home directory:**

        cd ~

        git clone -b v23.10.0 https://github.com/cisagov/Malcolm

![](_images/136._Clone_Malcolm_Repo_v23.10.0.PNG)

*Figure 136 - RESPOND: Clone Malcolm Repository*

### 11.3.2 - Configure Malcolm

1. **In a terminal session, issue the following commands to initialize the Malcolm configuration script:**

        cd ~/Malcolm

        ./scripts/install.py --configure

2. **You'll be asked a series of questions to configure the Malcolm platform (answers in brackets):**

    1. "Add a non-root user to the 'docker' group?" **[Y]**
    2. "User:" **[your admin username]**
    3. The next 10 questions will be about modifying specific parameters for Malcolm (all beginning with *"fs."*, *"vm."*, and *"net.core."*). Answer **[Y]** to all 10 questions.
    4. "Malcolm processes will run as UID 1000 and GID 1000. Is this OK?" **[Y]**
    5. "Enter the node name to associate with network traffic metadata:" **[your RESPOND node name] (e.g. kali-eminence)**
    6. "Run with Malcolm (all containers) or Hedgehog (capture only) profile?"**[Malcolm]**
    7. "Should Malcolm use and maintain its own OpenSearch instance?" **[Y]**
    8. "Compress OpenSearch index snapshots?" **[N]**
    9. "Forward Logstash logs to a secondary remote OpenSearch instance?" **[N]**
    10. "Setting 16g for OpenSearch and 2500m for Logstash. Is this OK?" **[Y]**
    11. "Setting 4 workers for Logstash pipelines. Is this OK?" **[Y]**
    12. "Restart Malcolm upon system or Docker daemon restart?" **[Y]**
    13. "Select Malcolm restart behavior" **[unless-stopped]**
    14. "Require encrypted HTTPS connections?" **[Y]**
    15. "Will Malcolm be running behind another reverse proxy (Traefik, Caddy, etc.)?" **[N]**
    16. "Specify external Docker network name (or leave blank for default networking)" **[leave blank]**
    17. "Select authentication method (Basic/LDAP/None)" [Basic]
    18. "Store PCAP, log and index files locally under /home/user/Malcolm?" **[Y]**
    19. "Should Malcolm delete the oldest database indices and/or PCAP files based on available storage?" **[N]**
    20. "Automatically analyze all PCAP files with Suricata? **[Y]**
    21. "Download updated Suricata signatures periodically?" **[Y]**
    22. "Automatically analyze all PCAP files with Zeek?" **[Y]**
    23. "Is Malcolm being used to monitor an Operation Technology/Industrial Control Systems (OT/ICS) network?" **[N]**
    24. "Perform reverse DNS lookup locally for source and destination IP addresses in logs?" **[N]**
    25. "Perform hardware vendor OUI lookups for MAC addresses?" **[Y]**
    26. "Perform string randomness scoring on some fields?" **[Y]**
    27. "Should Malcolm accept logs and metrics from a Hedgehog Linux sensor or other forwarder?' **[N, unless you are deploying remote sensors, then answer [Y] to this question]**
    28. "Enable file extraction with Zeek?" **[Y]**
    29. "Select file extraction behavior" **[interesting]**
    30. "Select file preservation behavior" **[quarantine]**
    31. "Expose web interface for downloading preserved files?" **[Y]**
    32. "Enter AES-256-CBC encryption password for downloaded preserved files (or leave blank for unencrypted)" **[leave blank]**
    33. "Scan extracted files with ClamAV?" **[Y]**
    34. "Scan extracted files with Yara?" **[Y]**
    35. "Scan extracted files with Capa?" **[Y]**
    36. ""Lookup extracted file hashes with VirusTotal?" **[N]**
    37. "Download updated file scanner signatures periodically?" **[Y]**
    38. "Should Malcolm run and maintain an instance of NetBox, an infrastructure resource modelling tool?" **[Y]**
    39. "Should Malcolm enrich network traffic using NetBox?" **[Y]**
    40. "Should Malcolm automatically populate NetBox inventory based on observed network traffic?" [Y/N, your preference]
    41. "Specify default NetBox site name" [your choice of name for NetBox site] (e.g. NetBox)
    42. "Should Malcolm create 'catch-all' prefixes for private IP address space?" **[N]**
    43. "Should Malcolm capture live network traffic?" **[N]**
    44. "Enable dark mode for OpenSearch Dashboards?" **[Y]**
3. **In the terminal session, issue the following command to reboot your RESPOND node:**

        sudo reboot

4. **Once your RESPOND node has rebooted, log back in and re-launch a terminal session.**

5. **In the terminal session, issue the following commands to set up Malcolm authentication:**

        cd ~/Malcolm

        ./scripts/auth_setup

6. **You'll be asked a series of questions to configure the Malcolm platform (answers in brackets):**

    1. "Configure Authentication" **[all]**
    2. "Store administrator username/password for local Malcolm access?" **[Y]**
    3. "Administrator username" **[your choice] - NOTE: This is a local user account within Malcolm. It will have administrative privileges within the Malcolm ecosystem.**
    4. " [admin_account] Password" **[your choice]**
    5. " [admin_account] Password (again)" **[confirm your choice]**
    6. "(Re)generate self-signed certificates for HTTPS access?" **[Y]**
    7. "(Re)generate self-signed certificates for a remote log forwarder?" **[Y]**
    8. "Store username/password for primary remote OpenSearch instance?" **[N]**
    9. "Store username/password for OpenSearch Alerting email sender account?" **[N]**
    10. "(Re)generate internal passwords for NetBox?" **[Y]**
    11. "Store password hash secret for Arkime viewer cluster?" **[N]**

### 11.3.3 - Pull Malcolm Docker Images

1. **In a terminal session, issue the following commands to pull Malcolm Docker images:*

        cd ~/Malcolm

        docker-compose --profile malcolm pull

![](_images/137._Pull_Malcolm_Docker_Images.PNG)

*Figure 137 - DOCKER: Pull Malcolm images*

1. **When Docker is done pulling the images, you can check for the 19 Malcolm images by using the following command:**

        sudo docker image ls

 ![](_images/138._Verify_Malcolm_Images.PNG)

*Figure 138 - RESPOND: Malcolm Docker Images*

### 11.3.4 - Start Malcolm

**In a terminal session, issue the following commands to start Malcolm:**

        cd ~/Malcolm

        ./scripts/start

 ![](_images/139._Start_Malcolm.PNG)

*Figure 139 - RESPOND: Start Malcolm Services*

### 11.3.5 - Verify Malcolm Web Services

1. **On your DETECT node, open a browser and browse to your IDENTIFY node's Portainer portal (e.g. https://kali-violet.kali.purple:9443). Log into Portainer using your admin credentials.**

![](_images/126._Log_into_Portainer.png)

2. **Click on the local environment in the center (the Docker icon). Then on the left side of the next page int the navigation area, select "Stacks."**

![](_images/139a._Select_Stacks.png)

3. **On the "Stacks" page, select "malcolm." All containers listed should be green and healthy.**

![](_images/140._Malcolm_Containers_in_Portainer.PNG)

*Figure 140 - RESPOND: Malcolm Docker Containers*

4. **Browse the web applications. These will be accessed at the FQDN of your RESPOND node. In this document, my node is kali-eminence.kali.purple.**

    1. **Malcolm Readme:** https://kali-eminence.kali.purple/readme
    2. **Arkime Full Packet Capture:** https://kali-eminence.kali.purple
    3. **GCHQ's CyberChef:** https://kali-eminence.kali.purple/cyberchef.html
    4. **Malcolm's Opensearch Dashboards:** https://kali-eminence.kali.purple/dashboards **(NOTE: These may initially throw errors due to there being no data present)**
    5. **Netbox:** https://kali-eminence.kali.purple/netbox
    6. **Malcolm Network Traffic Artifact Upload:** https://kali-eminence.kali.purple/upload
    7. **Malcolm User Management:** https://kali-eminence.kali.purple/auth

 ![](_images/141._Malcolm_Readme.PNG)

*Figure 141 - RESPOND: Malcolm ReadMe*

![](_images/142._Arkime_Sessions_Interface.PNG)

*Figure 142 - RESPOND: Arkime Sessions Page*

![](_images/143._CyberChef.PNG)

*Figure 143 - RESPOND: CyberChef*

![](_images/144._Malcolm_Opensearch_Dashboards.PNG)

*Figure 144 - RESPOND: Opensearch Dashboards*

![](_images/145._NetBox_Interface.PNG)

*Figure 145 - RESPOND: NetBox*

![](_images/146._Network_Artifact_Upload.PNG)

*Figure 146 - RESPOND: Malcolm Network Traffic Artifact Upload*

![](_images/147._Malcolm_User_Management.PNG)

*Figure 147 - RESPOND: Malcolm User Account Management*

### 11.3.6 - Create Sensor Account(s) (only if deploying remote sensors)

1. **In a browser, navigate to the Malcolm User Account Management page on your RESPOND node using HTTPS (e.g. https://kali-eminence.kali.purple/auth). Log in with the credentials you created during the Malcolm configuration.**

2. **Create user accounts for any sensors to authenticate with the RESPOND node. You can create one per sensor, or one for all sensors.**

3. **When complete, log out of the application.**

---
<div align="center">
| [Previous Section: 10 - Configure: IDENTIFY Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/10%20-%20Configure%20IDENTIFY%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/README.md) | [Next Section: 12 - Build and Configure: Remote Network Sensor](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/12%20-%20Build%20and%20Configure%20-%20Remote%20Network%20Sensor.md) |
</div>