# 9 - Configure: DETECT Node

In this section, we will build the heart of this SOC solution, the Elastic SIEM node. In line with the Proxmox instructions, this node is named "kali-purple." Since all other nodes will be sending data to the Elasticsearch instance that will reside on this system, I chose to build it second, after the firewall. If you recall, the firewall (byzantium) is already configured to forward syslog data to this node. Once we get this node configured, it should automatically start ingesting the syslog data from the firewall.

<details>
<summary><h2>9.1 - Elastic Stack SIEM Installation and Configuration</h2></summary>

### 9.1.1 - Install Elasticsearch

1. **Log in to the DETECT node with your user credentials.**

![](_images/74._Kali_Purple_Login.PNG)

2. **Launch a terminal session.**

![](_images/75._Terminal.PNG)

3. **In the terminal session, issue the following command to install Elasticsearch:**

        sudo bash -c "export HOSTNAME=kali-purple.kali.purple; apt-get install elasticsearch=8.11.3 -y"

![](_images/82._Install_ES.PNG)

*Figure 82 - DETECT: Install Elasticsearch*

**Take note of the password that is generated for the built-in superuser 'elastic.' You will need this in later steps.**

![](_images/83._ES_Password.PNG)

*Figure 83 - DETECT: Elasticsearch password*

4. **In the terminal session, enter the following commands to convert the installation into a single-node setup:**

        sudo sed -e '/cluster.initial_master_nodes/ s/^#*/#/' -i /etc/elasticsearch/elasticsearch.yml
        echo "discovery.type: single-node" | sudo tee -a /etc/elasticsearch/elasticsearch.yml

![](_images/84._Modify_etc-elasticsearch-elasticsearch.yml.png)

*Figure 84 - DETECT: Configure Elasticsearch as single-node*

5. **Keep this terminal session open for the next section.**

### 9.1.2 - Install Kibana

1. **In the terminal session, issue the following command to install Kibana:**

        sudo apt install kibana=8.11.3 -y

![](_images/85._Install_Kibana.PNG)

*Figure 85 - DETECT: Install Kibana*

2. **In the terminal session, issue the following command to generate Kibana encryption keys and enter them into the /etc/kibana/kibana.yml file:**

        sudo /usr/share/kibana/bin/kibana-encryption-keys generate -q | sudo tee -a /etc/kibana/kibana.yml

![](_images/86._Generate_Kibana_Encryption_Keys.PNG)

*Figure 86 - DETECT: Kibana generate HTTP encryption keys*

3. **In the terminal session, issue the following command to amend the /etc/kibana/kibana.yml file with a "server.host" entry:**

        echo "server.host: \"kali-purple.kali.purple\"" | sudo tee -a /etc/kibana/kibana.yml

![](_images/86a._Modify_kibana-yml.PNG)

4. **Verify that there is ONLY ONE entry in the /etc/hosts file that maps the configured IP address to the configured hostname. There should be no other entries tied to the hostname. This will allow Kibana to specifically bind to that interface/IP address.**

![](_images/87._Verify_hosts_file.PNG)

*Figure 87 - DETECT: Verifying /etc/hosts file*

5. **In the terminal session, issue the following command to configure Elasticsearch and Kibana for automatic startup:**

        sudo systemctl enable elasticsearch kibana --now

![](_images/88._Auto-start_ES_-_Kibana.PNG)

*Figure 88 - DETECT: Enable auto-start for Elasticsearch/Kibana*

6. **In the terminal session, issue the following command to enroll Kibana in Elasticsearch:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

![](_images/89._Kibana_Enrollment_Token.PNG)

*Figure 89 - DETECT: Generate Kibana enrolment token*

**Copy this token, as you will need it for the next step.**

7. **Open a browser and navigate to http://kali-purple.kali.purple:5601. NOTE: The URL is HTTP, not HTTPS. You will be immediately prompted for the Kibana enrolment token. Paste the toke from step 6 of this section into the window supplied and then click "Configure Elastic."**

![](_images/90._Kibana_Enrollment_Token.PNG)

*Figure 90 - DETECT: Enrolling Kibana*

8. **You will be prompted for a verification code. In the terminal session, issue the following command to generate a verification code:**

        sudo /usr/share/kibana/bin/kibana-verification-code

![](_images/91._Kibana_Verification_Code.PNG)

*Figure 91 - DETECT: Generating Kibana verification code*

9. **Enter the verification code from step 8 into the browser and click "Verify." In a few seconds, you'll be redirected to a "Welcome to Elastic" screen with a login form. DO NOT log in here.**

![](_images/92._Kibana_Verification_Code.PNG)

*Figure 92 - DETECT: Verifying Kibana*

![](_images/92a._Kibana_Login_Screen.PNG)

### 9.1.3 - Enable HTTPS for Kibana

For this next section, you'll need to create a password to use for SSL certificates. The method I used was to generate a single secure password (for ease of configuration, as this is a lab environment) and use it for all password prompts for this section.

1. **In the terminal session, issue the following command to generate a new certificate authority and X.509 certificate and private key:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-certutil ca

![](_images/93._SSL.PNG)

*Figure 93 - DETECT: Generating new CA*

**(a)  Accept the default name for the output file (elastic-stack-ca.p12).**

**(b)  Enter your secure password.**

2. **In the terminal session, issue the following command to generate the Kibana PKCS#12 certificate and private key:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12 --dns kali-purple.kali.purple,elastic.kali.purple,kali-purple --out kibana-server.p12

![](_images/94._SSL.PNG)

*Figure 94 - DETECT: Generating Kibana PKCS#12 certificate*

**(a)  Enter your secure password.**

3. **In the terminal session, issue the following commands to write the client certificates (enter your secure password after each command):**

        sudo openssl pkcs12 -in /usr/share/elasticsearch/elastic-stack-ca.p12 -clcerts -nokeys -out /etc/kibana/kibana-server_ca.crt
        sudo openssl pkcs12 -in /usr/share/elasticsearch/kibana-server.p12 -out /etc/kibana/kibana-server.crt -clcerts -nokeys
        sudo openssl pkcs12 -in /usr/share/elasticsearch/kibana-server.p12 -out /etc/kibana/kibana-server.key -nocerts -nodes

![](_images/95._SSL.PNG)

*Figure 95 - DETECT: Writing client certificates*

4. **In the terminal session, issue the following commands to change ownership of the new client SSL certificates to the root user, as well as modify the permissions:**

        sudo chown root:kibana /etc/kibana/kibana-server_ca.crt
        sudo chown root:kibana /etc/kibana/kibana-server.key
        sudo chown root:kibana /etc/kibana/kibana-server.crt
        sudo chmod 660 /etc/kibana/kibana-server_ca.crt
        sudo chmod 660 /etc/kibana/kibana-server.key
        sudo chmod 660 /etc/kibana/kibana-server.crt
 
 ![](_images/96._SSL.PNG)

*Figure 96 - DETECT: Changing owner/permissions for client certs*

5. **In the terminal session, issue the following commands to modify the file /etc/kibana/kibana.yml:**

        echo "server.ssl.enabled: true" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.ssl.certificate: /etc/kibana/kibana-server.crt" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.ssl.key: /etc/kibana/kibana-server.key" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.publicBaseUrl: \"https://kali-purple.kali.purple:5601\"" | sudo tee -a /etc/kibana/kibana.yml

![](_images/97._SSL.PNG)

*Figure 97 - DETECT: Modifying /etc/kibana/kibana.yml*

6. **In the terminal session, issue the following command to comment out the previous encryption keys entered into /etc/kibana/kibana.yml:**

        sudo sed -e '/encryptionKey/ s/^#\*/#/' -i /etc/kibana/kibana.yml

7. **In the terminal session, issue the following command to generate Kibana encryption keys and enter them into the /etc/kibana/kibana.yml file:**

        sudo /usr/share/kibana/bin/kibana-encryption-keys generate -q --force| sudo tee -a /etc/kibana/kibana.yml

![](_images/98._SSL.PNG)

*Figure 98 - DETECT: Generating Kibana SSL encryption keys*

8. **In the terminal session, issue the following command to restart the Kibana service:**

        sudo systemctl restart kibana

![](_images/99._Restart_Kibana.PNG)

*Figure 99 - DETECT: Restart Kibana*

9. **In a few seconds, use a browser to navigate to https://kali-purple.kali.purple:5601. You'll be greeted by a page warning of an untrusted site (due to a self-signed certificate). Accept the risk to continue, and you will be redirected to the Kibana logon page (HTTPS).**

![](_images/99a._Kibana_over_TLS.PNG)

10. **Log in with the username 'elastic' and the password you recorded from [Section 9.1.1](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/9%20-%20Configure%20DETECT%20Node.md#911-install-elasticsearch) step 3.**

![](_images/99b._Kibana_Login.PNG)

*Figure 100 - DETECT: Kibana Login*

11. **You will see the "Welcome to Elastic" page. Click "Explore on my own."**

![](_images/99c._Kibana_Splash.PNG)

12. **On the next page, in the upper right corner, click the circled "E." This will reveal a drop-down menu. Click "Edit Profile."**

![](_images/100._User_Settings.PNG)

13. **On the "Profile" page, set a secure password for the 'elastic' superuser using the "Change password" button.**

![](_images/100a._User_Settings.PNG)
![](_images/101._User_Settings.PNG)

*Figure 101 - DETECT: Change Elastic user password*
</details>

<details>
<summary><h2>9.2 - Fleet Server</h2></summary>

### 9.2.1 - Installation

1. **Within the Kibana interface, click the navigation button in the upper left corner, scroll down towards the bottom of the list, and click "Fleet."**

![](_images/101b._Navigate_to_Fleet.PNG)

2. **On the "Fleet" page, click the "Add Fleet Server" button.**

![](_images/101c._Add_Fleet_Server.PNG)

3. **On the page that appears, enter a name for your Fleet Server Policy in the "Name" field. For the URL, enter the HTTPS FQDN of your DETECT node, at port 8220. In my example, I will enter https://kali-purple.kali.purple:8220. Then, click "Generate Fleet Server policy."**

![](_images/102._New_Fleet_Server_Policy.PNG)

*Figure 102 - DETECT: Fleet server policy*

4. **Once the policy has been generated, the page will provide a set of commands to paste into a terminal session for execution. Copy the command using the clipboard icon provided in the upper right corner of the command window.**

![](_images/102a._Copy_Fleet_Server_Install_Script.PNG)

5. **In a terminal session, paste the command and add " --force" on the end to auto-confirm installation of the Elastic Agent. Make sure there is a space between the port 8220 and the "--force" option. If you wish, you can change the IP address to your DETECT node's FQDN for the *fleet-server-es* option.**

![](_images/103._Paste_Fleet_Install_Command.PNG)

*Figure 103 - DETECT: Fleet server installation*

6. **When complete, you'll see a confirmation message in the terminal session, and the fleet server installation page will have 3 green checkmarks indicating that the fleet server has connected. DO NOT select the "Continue enrolling Elastic Agent." Simply click the small "x" in the upper right corner to close the sub-window.**

![](_images/103a._Fleet_Installation_Complete.PNG)
![](_images/103b._Fleet_Installation_Complete.PNG)

7. **Back on the Fleet page, click the "Agent policies" tab, and select your fleet server policy.**

![](_images/103c._Fleet_Server_Policy.PNG)

8. **On the next page, select "Add integration."**

![](_images/103d._Add_integration.PNG)

9. **On the Integrations page, select the "Elastic Stack" category on the left side, and then "Elastic Agent" that appears on the right.**

![](_images/103d._Elastic_Agent.PNG)

10. **On the Elastic Agent screen, select "Add Elastic Agent" in the upper right.**

![](_images/103e._Elastic_Agent.PNG)

11. **No changes are needed on this page. Just click the "Save and continue" button in the lower right.**

![](_images/103f._Elastic_Agent.PNG)

12. **Next, click "Save and deploy changes."**

![](_images/103g._Elastic_Agent.PNG)

13. **Back on your fleet server policy page, you'll now see 3 integrations (elastic\_agent-1, fleet\_server-1, and system-1). Click the "Add integration" button again to add the pfSense module. This will allow the fleet server to ingest and properly parse the syslog data from the PROTECT node.**

![](_images/103h._Updated_Fleet_Server_Policy.PNG)

14. **In the search bar of the Integrations page, search "pfsense." Click the pfSense integration that appears.**

![](_images/103i._pfSense_Integration.PNG)

15. **On the "Add pfSense integration" page, enter "0.0.0.0" into the "Syslog Host" field, and ensure that the port is set to 9001. Click "Save and continue" in the lower right.**

![](_images/103j._pfSense_Integration.PNG)

16. **Click "Save and deploy changes."**

![](_images/103k._pfSense_Integration.PNG)

17. **Now your fleet server policy has the pfSense integration added. To verify that it is ingesting data from the PROTECT node, use the navigation on the left to go to Analytics -\> Discover.**

![](_images/103l._Discover.PNG)

18. **In the search bar of the Discover page, enter the following search string:**

        data_stream.dataset : "pfsense.log"

**If the DETECT node is ingesting properly, you will see data coming in.**

![](_images/104._OPNsense_data_being_ingested.PNG)

*Figure 104 - DETECT: Kibana Discover*

### 9.2.2 - Linux Server Policy

1. **Within the Kibana interface, click the navigation button in the upper left corner, and navigate to Management -\> Fleet.**

![](_images/104a._Nav_to_Fleet.PNG)

2. **On the Fleet screen, select "Agent Policies." On the right, click "Create agent policy."**

![](_images/104b._New_Agent_Policy.PNG)

3. **For the name of the policy, enter "Linux Server Policy." Click the "Create agent policy" button in the lower right.**

![](_images/104c._Create_Policy.PNG)

4. **On the right side of the screen, click "Add integration."**

![](_images/104d._Add_integration.PNG)

5. **On the Integrations page, select the "Elastic Stack" category on the left side, and then "Elastic Agent" that appears on the right.**

![](_images/103d._Elastic_Agent.PNG)

6. **On the Elastic Agent screen, select "Add Elastic Agent" in the upper right.**

![](_images/103e._Elastic_Agent.PNG)

7. **Ensure the newly created Linux policy is selected for "Agent Policy." Click the "Save and continue" button in the lower right.**

![](_images/104e._Add_Elastic_Agent.PNG)

8. **Next, click "Add Elastic Agent later."**

![](_images/104f._Add_Agent_later.PNG)

9. **You will be returned to the "Linux Server Policy" page. There will be a window reminding you about adding Elastic Agent to your hosts. Click "Got it" to close. Your Linux policy should now show two integrations, the system (default) and the newly added Elastic Agent. We will install this agent manually on the IDENTIFY and RESPOND nodes later.**

![](_images/104g._Add_Agent_later.PNG)
![](_images/105._Linux_Server_policy.PNG)

*Figure 105 - DETECT: Linux server policy*
</details>

<details>
<summary><h2>9.3 - Beats</h2></summary>

### 9.3.1 - Collect Elasticsearch Certificate Fingerprint

1. **In a terminal session, issue the following command to obtain the Elasticsearch certificate fingerprint on the DETECT node:**

        sudo openssl x509 -fingerprint -sha256 -noout -in /etc/elasticsearch/certs/http_ca.crt | awk 'BEGIN { FS = "=" } ; { print $2 }' | sed 's/://g'

![](_images/106._ES_Fingerprint.PNG)

*Figure 106 - DETECT: Acquiring Elasticsearch fingerprint*

**Copy the fingerprint value as you will need it for Metricbeat and Filebeat configuration.**

### 9.3.2 - Metricbeat Installation and Configuration

1. **In a terminal session, enter the following commands to download and install Metricbeat:**

        cd ~
        curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.11.3-amd64.deb
        sudo dpkg -i metricbeat-8.11.3-amd64.deb

![](_images/107._Install_Metricbeat.PNG)

*Figure 107 - DETECT: Installing Metricbeat*

2. **In a terminal session, issue the following command to edit the file /etc/metricbeat/metricbeat.yml:**

        sudo nano /etc/metricbeat/metricbeat.yml

2.1 - **Find the section titled "Kibana."**

2.1.1 - **Uncomment the line *#host: "localhost:5601"*. Change it to read:**

        host: "https://kali-purple.kali.purple"

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2.1.2 - **Immediately below this line, add the following lines (all aligned with the previous line):**

        setup.kibana.ssl.enabled: true
        ssl.certificate_authorities: ["/etc/kibana/kibana-server_ca.crt"]
        setup.kibana.ssl.certificate: "/etc/kibana/kibana-server.crt"
        setup.kibana.ssl.key: "/etc/kibana/kibana-server.key"

2.2 - **Find the section titled "Elasticsearch Output."**

2.2.1 - **Uncomment the line *#hosts: ["localhost:9200"]*. Change it to read:**

        hosts: ["https://kali-purple.kali.purple"]

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2.2.2 - **Uncomment the line in this section beginning with *#username*. Change it to read:**

        username: "elastic"

2.2.3 - **Uncomment the line in this section beginning with *#password*. Change it to reflect your 'elastic' user password. Ensure your password is contained within quotation marks like the following example:**

        password: "s3cret_p@$$w0rd"

2.2.4 - **Immediately below the password line, add the following line (verify alignment):**

        ssl.ca_trusted_fingerprint: "<your Elasticsearch fingerprint value>"
        
![](_images/108._Modify_etc-metricbeat-metricbeat-yml.PNG)

*Figure 108 - DETECT: Modifying /etc/metricbeat/metricbeat.yml*

2.3 - **Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.**

3. **In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch:**

        sudo metricbeat modules enable elasticsearch

![](_images/108a._Enable_Elasticsearch_module.PNG)

4. **In a terminal session, issue the following command to edit the file /etc/metricbeat/modules.d/elasticsearch.yml:**

        sudo nano /etc/metricbeat/modules.d/elasticsearch.yml

4.1 - **Uncomment the line that begins with *#hosts* if it is commented. Change it to read:**

        hosts: ["https://kali-purple.kali.purple:9200"]

**\*NOTE THE INCLUSION OF THE PORT NUMBER**

4.2 - **Uncomment the line that begins with *#username* if it is commented. Change it to read:**

        username: "elastic"

4.3 - **Uncomment the line that begins with *#password* if it is commented. Change it to reflect your 'elastic' user's password.**

![](_images/109._Modify_etc-metricbeat-modules.d-elasticsearch-yml.PNG)

*Figure 109 - DETECT: Modifying /etc/metricbeat/modules.d/elasticsearch.yml*

4.4 - **Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.**

5. **In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch XPack:**

        sudo metricbeat modules enable elasticsearch-xpack

![](_images/109a._Enable_Elasticsearch-XPack_module.PNG)

6. **In a terminal session, issue the following command to edit the file /etc/metricbeat/modules.d/elasticsearch-xpack.yml:**

        sudo nano /etc/metricbeat/modules.d/elasticsearch-xpack.yml

6.1 - **Uncomment the line that begins with *#hosts* if it is commented. Change it to read:**

        hosts: ["https://kali-purple.kali.purple:9200"]

**\*NOTE THE INCLUSION OF THE PORT NUMBER**

6.2 - **Immediately below this line, enter the following line (ensure proper alignment):**

        protocol: "https"

6.3 - **Uncomment the line that begins with *#username* if it is commented. Change it to read:**

        username: "elastic"

6.4 - **Uncomment the line that begins with *#password* if it is commented. Change it to reflect your 'elastic' user's password.**

6.5 - **Immediately below this line, add the following lines (ensure proper alignment, indentions are 2 spaces):**

        ssl:
          enabled: true
          ca_trusted_fingerprint: "<your Elasticsearch fingerprint value>"
          verification_mode: "certificate"

![](_images/110._Modify_etc-metricbeat-modules.d-elasticsearch-xpack-yml.PNG)

*Figure 110 - DETECT: Modifying /etc/metricbeat/modules.d/elasticsearch-xpack.yml*

6.6 - **Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.**

7. **In a terminal session, issue the following command to test the Metricbeat configuration:**

        sudo metricbeat test config

![](_images/111._Test_Config.PNG)

*Figure 111 - DETECT: Test Metricbeat configuration*

8. **In a terminal session, issue the following command to test the Metricbeat modules:**

        sudo metricbeat test modules

![](_images/112._Test_Modules.PNG)

*Figure 112 - DETECT: Test Metricbeat modules*


**NOTE : The first 7 or so lines may show errors upon execution. This will not affect the success of the installation. This step may take a few seconds to complete.**

9. **In a terminal session, issue the following command to set up Metricbeat index patterns and load dashboards into Kibana:**

        sudo metricbeat setup

![](_images/113._Metricbeat_setup.PNG)

*Figure 113 - DETECT: Metricbeat setup*

**This will take a few minutes to complete.**

10. **In the terminal session, issue the following command to configure Metricbeat for automatic startup:**

        sudo systemctl enable metricbeat --now
        
![](_images/113a._Enable_Metricbeat.PNG)

11. **In Kibana (in a browser), use the navigation on the left to go to Management -\> Stack Monitoring.**

![](_images/113b._Navigate_to_Stack_Monitoring.PNG)

12. **On the next screen, select "Set up monitoring with Metricbeat."**

![](_images/113c._Set_Up_monitoring.PNG)

13. **Click "Monitor with Metricbeat" below the DETECT node's name.**

![](_images/113d._Monitor_with_Metricbeat.PNG)

14. **Click "Next."**

15. **Ensure "Yes" is selected on the "Create rules" window, and click "Continue."**

![](_images/113e._Monitor_with_Metricbeat.PNG)

16. **Click "Exit setup mode" at either the top or the bottom of the window on the right side.**

### 9.3.3 - Filebeat Installation and Configuration

1. **In a terminal session, enter the following commands to download and install Filebeat:**

        cd ~
        curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.11.3-amd64.deb
        sudo dpkg -i filebeat-8.11.3-amd64.deb

![](_images/114._Install_Filebeat.PNG)

*Figure 114 - DETECT: Install Filebeat*

2. **In a terminal session, issue the following command to edit the file /etc/filebeat/filebeat.yml:**

        sudo nano /etc/filebeat/filebeat.yml

2.1 - **Find the section titled "Kibana."**
2.1.1 - **Uncomment the line *#host: "localhost:5601"*. Change it to read:**

        host: "https://kali-purple.kali.purple"

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2.1.2 - **Immediately below this line, add the following lines (all aligned with the previous line):**

        setup.kibana.ssl.enabled: true
        ssl.certificate_authorities: ["/etc/kibana/kibana-server_ca.crt"]
        setup.kibana.ssl.certificate: "/etc/kibana/kibana-server.crt"
        setup.kibana.ssl.key: "/etc/kibana/kibana-server.key"

2.2 - **Find the section titled "Elasticsearch Output."**

2.2.1 - **Uncomment the line *#hosts: ["localhost:9200"]*. Change it to read:**

        hosts: ["https://kali-purple.kali.purple"]

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2.2.2 - **Uncomment the line in this section beginning with *#username*. Change it to read:**

        username: "elastic"

2.2.3 - **Uncomment the line in this section beginning with *#password*. Change it to reflect your 'elastic' user password. Ensure your password is contained within quotation marks like the following example:**

        password: "s3cret_p@$$w0rd"
        
2.2.4 - **Immediately below the password line, add the following line (verify alignment):**

        ssl.ca_trusted_fingerprint: "<your Elasticsearch fingerprint value>"

![](_images/115._Modify_etc-filebeat-filebeat-yml.PNG)

*Figure 115 - DETECT: Modify /etc/filebeat/filebeat.yml*

2.3 - **Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.**

3. **In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch:**

        sudo filebeat modules enable elasticsearch

![](_images/115a._Enable_Elasticsearch_module.PNG)

4. **In a terminal session, issue the following command to edit the file /etc/filebeat/modules.d/elasticsearch.yml:**

        sudo sed -i 's/false/true/g' /etc/filebeat/modules.d/elasticsearch.yml

![](_images/116._Modify_etc-filebeat-modules.d-elasticsearch-yml.PNG)

*Figure 116 - DETECT: Modify /etc/filebeat/modules.d/elasticsearch.yml*

5. **In a terminal session, issue the following command to set up Filebeat index patterns and load dashboards into Kibana:**

        sudo filebeat setup

![](_images/117._Filebeat_setup.PNG)

*Figure 117 - DETECT: Filebeat setup*

 **This will take a few minutes to complete.**

6. **In the terminal session, issue the following command to configure Filebeat for automatic startup:**

        sudo systemctl enable filebeat --now

![](_images/117a._Enable_Filebeat.PNG)

7. **In Kibana (in a browser), use the navigation on the left to go to Management -\> Dev Tools.**

![](_images/117b._Dev_Tools.PNG)

8. **Delete the data in the left pane and enter the following:**

        PUT _settings
        {
        "number_of_replicas": 0
        }

**NOTE 1: There is a space between "PUT" and "\_settings"**

**NOTE 2: Line 3 is indented 2 spaces, and ends with the number 0.**

**NOTE 3: The last line with the "}" has no empty space/line after it.**

9. **Press the green triangle on the right side of the left pane to execute the command.**

![](_images/117c._Change_Replica_Shard_Value.PNG)

10. **Upon successful execution, the right pane will have a green "200 OK" above it, with the following message in the pane:**

        {
        "acknowledged": true
        }
 
 ![](_images/117d._Change_Replica_Shard_Value.PNG)
 </details>

---
<div align="center">
| [Previous Section: 8 - Kali Purple](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/8%20-%20Kali%20Purple.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/README.md) | [Next Section: 10 - Configure: IDENTIFY Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/10%20-%20Configure%20IDENTIFY%20Node.md) |
</div>