# Configure: RESPOND Node

  1.
## Elastic Agent

To install the Elastic Agent on this host, follow the instructions in section 10.1.1 (Elastic Agent).

  1.
## Docker and Malcolm Prerequisites

    1.
### Installation

      1. In a terminal session, issue the following command to install Docker and prerequisite packages for Malcolm:
**sudo apt install nginx apache2-utils python3-pip python3-requests python3-dialog apt-transport-https gnupg-agent software-properties-common docker.io docker-compose**
 ![](RackMultipart20231221-1-5l8g4e_html_71c018d721368def.png)

Figure 135 - RESPOND: Docker install with Malcolm Prerequisites

      1. In the terminal session, issue the following command to set the Docker service to automatically start:
**sudo systemctl enable docker --now**


    1.
### Portainer

To install the Portainer on this host, follow the instructions in section 10.2.2 (Portainer). Make sure to replace any references for your IDENTIFY node with your RESPOND node (name/IP address).

  1.
## INL Malcolm

    1.
### Clone Malcolm Repository

In a terminal session, issue the following commands to clone the INL Malcolm repository (version 23.10.0) to your home directory:
**cd ~
 git clone -b v23.10.0 https://github.com/cisagov/Malcolm**
 ![](RackMultipart20231221-1-5l8g4e_html_6243f94a64b8a496.png)

Figure 136 - RESPOND: Clone Malcolm Repository

    1.
### Configure Malcolm

      1. In a terminal session, issue the following commands to initialize the Malcolm configuration script:
**cd ~/Malcolm
 ./scripts/install.py --configure**
      2. You'll be asked a series of questions to configure the Malcolm platform (answers in brackets]:
        1. "Add a non-root user to the 'docker' group?" [Y]
        2. "User:" [your admin username]
        3. The next 10 questions will be about modifying specific parameters for Malcolm (all beginning with "fs.", "vm.", and "net.core."). Answer [Y] to all 10 questions
        4. "Malcolm processes will run as UID 1000 and GID 1000. Is this OK?" [Y]
        5. "Enter the node name to associate with network traffic metadata:" [your RESPOND node name] (e.g. kali-eminence)
        6. "Run with Malcolm (all containers) or Hedgehog (capture only) profile?" [Malcolm]
        7. "Should Malcolm use and maintain its own OpenSearch instance?" [Y]
        8. "Compress OpenSearch index snapshots?" [N]
        9. "Forward Logstash logs to a secondary remote OpenSearch instance?" [N]
        10. "Setting 16g for OpenSearch and 2500m for Logstash. Is this OK?" [Y]
        11. "Setting 4 workers for Logstash pipelines. Is this OK?" [Y]
        12. "Restart Malcolm upon system or Docker daemon restart?" [Y]
        13. "Select Malcolm restart behavior" [unless-stopped]
        14. "Require encrypted HTTPS connections?" [Y]
        15. "Will Malcolm be running behind another reverse proxy (Traefik, Caddy, etc.)?" [N]
        16. "Specify external Docker network name (or leave blank for default networking)" [leave blank]
        17. "Select authentication method (Basic/LDAP/None)" [Basic]
        18. "Store PCAP, log and index files locally under /home/user/Malcolm?" [Y]
        19. "Should Malcolm delete the oldest database indices and/or PCAP files based on available storage?" [N]
        20. "Automatically analyze all PCAP files with Suricata? [Y]
        21. "Download updated Suricata signatures periodically?" [Y]
        22. "Automatically analyze all PCAP files with Zeek?" [Y]
        23. "Is Malcolm being used to monitor an Operation Technology/Industrial Control Systems (OT/ICS) network?" [N]
        24. "Perform reverse DNS lookup locally for source and destination IP addresses in logs?" [N]
        25. "Perform hardware vendor OUI lookups for MAC addresses?" [Y]
        26. "Perform string randomness scoring on some fields?" [Y]
        27. "Should Malcolm accept logs and metrics from a Hedgehog Linux sensor or other forwarder?' [N, unless you are deploying remote sensors, then answer [Y] to this question]
        28. "Enable file extraction with Zeek?" [Y]
        29. "Select file extraction behavior" [interesting]
        30. "Select file preservation behavior" [quarantine]
        31. "Expose web interface for downloading preserved files?" [Y]
        32. "Enter AES-256-CBC encryption password for downloaded preserved files (or leave blank for unencrypted)" [leave blank]
        33. "Scan extracted files with ClamAV?" [Y]
        34. "Scan extracted files with Yara?" [Y]
        35. "Scan extracted files with Capa?" [Y]
        36. ""Lookup extracted file hashes with VirusTotal?" [N]
        37. "Download updated file scanner signatures periodically?" [Y]
        38. "Should Malcolm run and maintain an instance of NetBox, an infrastructure resource modelling tool?" [Y]
        39. "Should Malcolm enrich network traffic using NetBox?" [Y]
        40. "Should Malcolm automatically populate NetBox inventory based on observed network traffic?" [Y/N, your preference]
        41. "Specify default NetBox site name" [your choice of name for NetBox site] (e.g. NetBox)
        42. "Should Malcolm create 'catch-all' prefixes for private IP address space?" [N]
        43. "Should Malcolm capture live network traffic?" [N]
        44. "Enable dark mode for OpenSearch Dashboards?" [Y]
      3. In the terminal session, issue the following command to reboot your RESPOND node: **sudo reboot**
      4. Once your RESPOND node has rebooted, log back in and re-launch a terminal session.
      5. In the terminal session, issue the following commands to set up Malcolm authentication:
**cd ~/Malcolm
 ./scripts/auth\_setup**
      6. You'll be asked a series of questions to configure the Malcolm platform (answers in brackets]:
        1. "Configure Authentication" [all]
        2. "Store administrator username/password for local Malcolm access?" [Y]
        3. "Administrator username" [your choice] – NOTE: This is a local user account within Malcolm. It will have administrative privileges within the Malcolm ecosystem.
        4. " [admin\_account] Password" [your choice]
        5. " [admin\_account] Password (again)" [confirm your choice]
        6. "(Re)generate self-signed certificates for HTTPS access?" [Y]
        7. "(Re)generate self-signed certificates for a remote log forwarder?" [Y]
        8. "Store username/password for primary remote OpenSearch instance?" [N]
        9. "Store username/password for OpenSearch Alerting email sender account?" [N]
        10. "(Re)generate internal passwords for NetBox?" [Y]
        11. "Store password hash secret for Arkime viewer cluster?" [N]
    2.
### Pull Malcolm Docker Images

      1. In a terminal session, issue the following commands to pull Malcolm Docker images:
**cd ~/Malcolm
 docker-compose --profile malcolm pull**![](RackMultipart20231221-1-5l8g4e_html_cd80678e1c44ebbf.png)

Figure 137 - DOCKER: Pull Malcolm images

      1. When Docker is done pulling the images, you can check for the 19 Malcolm images by using the following command:
**sudo docker image ls**
 ![](RackMultipart20231221-1-5l8g4e_html_fe34aae1c840410.png)

Figure 138 - RESPOND: Malcolm Docker Images

    1.
### Start Malcolm

In a terminal session, issue the following commands to start Malcolm:
**cd ~/Malcolm
 ./scripts/start**
 ![](RackMultipart20231221-1-5l8g4e_html_b682f723526e2d79.png)

Figure 139 - RESPOND: Start Malcolm Services

    1.
### Verify Malcolm Web Services

      1. On your DETECT node, open a browser and browse to your IDENTIFY node's Portainer portal (e.g. https://kali-violet.kali.purple:9443). Log into Portainer using your admin credentials.
 ![](RackMultipart20231221-1-5l8g4e_html_23e719f58c7d6367.png)
      2. Click on the local environment in the center (the Docker icon). Then on the left side of the next page int the navigation area, select "Stacks."
 ![](RackMultipart20231221-1-5l8g4e_html_165cb8905874bd0c.png)
      3. On the "Stacks" page, select "malcolm." All containers listed should be green and healthy.
 ![](RackMultipart20231221-1-5l8g4e_html_87f3dc2f707e712b.png)

Figure 140 - RESPOND: Malcolm Docker Containers

      1. Browse the web applications. These will be accessed at the FQDN of your RESPOND node. In this document, my node is kali-eminence.kali.purple.
        1. Malcolm Readme: https://kali-eminence.kali.purple/readme
        2. Arkime Full Packet Capture: https://kali-eminence.kali.purple
        3. GCHQ's CyberChef: https://kali-eminence.kali.purple/cyberchef.html
        4. Malcolm's Opensearch Dashboards: https://kali-eminence.kali.purple/dashboards (NOTE: These may initially throw errors due to there being no data present)
        5. Netbox: https://kali-eminence.kali.purple/netbox
        6. Malcolm Network Traffic Artifact Upload: https://kali-eminence.kali.purple/upload
        7. Malcolm User Management: https://kali-eminence.kali.purple/auth
 ![](RackMultipart20231221-1-5l8g4e_html_4e5ddf8caf76d75b.png)

Figure 141 - RESPOND: Malcolm ReadMe

![](RackMultipart20231221-1-5l8g4e_html_ea95c5383b2a8ad9.png)

Figure 142 - RESPOND: Arkime Sessions Page

![](RackMultipart20231221-1-5l8g4e_html_7ab32b9766d8c11a.png)

Figure 143 - RESPOND: CyberChef

![](RackMultipart20231221-1-5l8g4e_html_b76c7f0f856864a8.png)

Figure 144 - RESPOND: Opensearch Dashboards

![](RackMultipart20231221-1-5l8g4e_html_b09dfa5b0ca4080f.png)

Figure 145 - RESPOND: NetBox

![](RackMultipart20231221-1-5l8g4e_html_ef077747941c256c.png)

Figure 146 - RESPOND: Malcolm Network Traffic Artifact Upload

![](RackMultipart20231221-1-5l8g4e_html_148fb06cbfed1065.png)

Figure 147 - RESPOND: Malcolm User Account Management

    1.
### Create Sensor Account (only if deploying remote sensors)

      1. In a browser, navigate to the Malcolm User Account Management page on your RESPOND node using HTTPS (e.g. https://kali-eminence.kali.purple/auth). Log in with the credentials you created during the Malcolm configuration.
      2. Create user accounts for any sensors to authenticate with the RESPOND node. You can create one per sensor, or one for all sensors.
      3. When complete, log out of the application.
      
---
<div align="center">
| [Previous Section: 10 - Configure: IDENTIFY Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/10%20-%20Configure%20IDENTIFY%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 12 - Build and Configure: Remote Network Sensor](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/12%20-%20Build%20and%20Configure%20-%20Remote%20Network%20Sensor.md) |
</div>