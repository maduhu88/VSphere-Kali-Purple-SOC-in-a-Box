# 9 - Configure: DETECT Node

In this section, we will build the heart of this SOC solution, the Elastic SIEM node. In line with the Proxmox instructions, we will name this node "kali-purple." Since all other nodes will be sending data to the Elasticsearch instance that will reside on this system, I chose to build it second, after the firewall. If you recall, the firewall (byzantium) is already configured to forward syslog data to this node. Once we get this node configured, it should automatically start ingesting the syslog data from the firewall.

## 9.1 - Elastic Stack SIEM Installation and Configuration

### 9.1.1 - Install Elasticsearch

1. **Log in to the DETECT node with your user credentials.**

![](RackMultipart20231221-1-5l8g4e_html_c939efd53b72c943.png)

2. **Launch a terminal session.**

![](RackMultipart20231221-1-5l8g4e_html_e9347639f7bfbaa4.png)

3. **In the terminal session, issue the following command to install Elasticsearch:**

        sudo bash -c "export HOSTNAME=kali-purple.kali.purple; apt-get install elasticsearch -y"

![](RackMultipart20231221-1-5l8g4e_html_2e7c994fb0b5754.png)

*Figure 82 - DETECT: Install Elasticsearch*

**Take note of the password that is generated for the built-in superuser 'elastic.' You will need this in later steps.**

![](RackMultipart20231221-1-5l8g4e_html_17eb3c0292ddf34e.png)

*Figure 83 - DETECT: Elasticsearch password*

1. **In the terminal session, enter the following commands to convert the installation into a single-node setup:**

        sudo sed -e '/cluster.initial\_master\_nodes/ s/^#\*/#/' -i /etc/elasticsearch/elasticsearch.yml
        echo "discovery.type: single-node" | sudo tee -a /etc/elasticsearch/elasticsearch.yml

![](RackMultipart20231221-1-5l8g4e_html_f8da48f272eeff2f.png)

*Figure 84 - DETECT: Configure Elasticsearch as single-node*

**Keep this terminal session open for the next section.**

### 9.1.2 - Install Kibana

1. **In the terminal session, issue the following command to install Kibana:**

        sudo apt install kibana -y

![](RackMultipart20231221-1-5l8g4e_html_a4bb11b8ef443baa.png)

*Figure 85 - DETECT: Install Kibana*

1. **In the terminal session, issue the following command to generate Kibana encryption keys and enter them into the /etc/kibana/kibana.yml file:**

        sudo /usr/share/kibana/bin/kibana-encryption-keys generate -q | sudo tee -a /etc/kibana/kibana.yml

![](RackMultipart20231221-1-5l8g4e_html_43fb9da9e14a589f.png)

*Figure 86 - DETECT: Kibana generate HTTP encryption keys*

1. **In the terminal session, issue the following command to amend the /etc/kibana/kibana.yml file with a "server.host" entry:**

        echo "server.host: \"kali-purple.kali.purple\"" | sudo tee -a /etc/kibana/kibana.yml

![](RackMultipart20231221-1-5l8g4e_html_8600d713f732d14b.png)

2. **Verify that there is ONLY ONE entry in the /etc/hosts file that maps the configured IP address to the configured hostname. There should be no other entries tied to the hostname. This will allow Kibana to specifically bind to that interface/IP address.**

![](RackMultipart20231221-1-5l8g4e_html_bd192d06dbdbbf5e.png)

*Figure 87 - DETECT: Verifying /etc/hosts file*

1. **In the terminal session, issue the following command to configure Elasticsearch and Kibana for automatic startup:**

        sudo systemctl enable elasticsearch kibana --now

![](RackMultipart20231221-1-5l8g4e_html_c56af7b7de755832.png)

*Figure 88 - DETECT: Enable auto-start for Elasticsearch/Kibana*

1. **In the terminal session, issue the following command to enroll Kibana in Elasticsearch:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

![](RackMultipart20231221-1-5l8g4e_html_25c09e25c2269b27.png)

*Figure 89 - DETECT: Generate Kibana enrolment token*

**Copy this token, as you will need it for the next step.**

1. **Open a browser and navigate to http://kali-purple.kali.purple:5601. NOTE: The URL is HTTP, not HTTPS. You will be immediately prompted for the Kibana enrolment token. Paste the toke from step 6 of this section into the window supplied and then click "Configure Elastic."**

![](RackMultipart20231221-1-5l8g4e_html_45c18285a9a6595a.png)

*Figure 90 - DETECT: Enrolling Kibana*

1. **You will be prompted for a verification code. In the terminal session, issue the following command to generate a verification code:**

        sudo /usr/share/kibana/bin/kibana-verification-code

![](RackMultipart20231221-1-5l8g4e_html_938f2ce2acb16f3a.png)

*Figure 91 - DETECT: Generating Kibana verification code*

1. **Enter the verification code from step 8 into the browser and click "Verify." In a few seconds, you'll be redirected to a "Welcome to Elastic" screen with a login form. DO NOT log in here.**

![](RackMultipart20231221-1-5l8g4e_html_b5b9a5548ce92ccd.png)

*Figure 92 - DETECT: Verifying Kibana*

![](RackMultipart20231221-1-5l8g4e_html_7f3c7835d10e8dc1.png)

### 9.1.3 - Enable HTTPS for Kibana

For this next section, you'll need to create a password to use for SSL certificates. The method I used was to generate a single secure password (for ease of configuration, as this is a lab environment) and use it for all password prompts for this section.

1. **In the terminal session, issue the following command to generate a new certificate authority and X.509 certificate and private key:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-certutil ca

![](RackMultipart20231221-1-5l8g4e_html_118b938af96700b3.png)

*Figure 93 - DETECT: Generating new CA*

1. **Accept the default name for the output file (elastic-stack-ca.p12).**

2. **Enter your secure password.**

1. **In the terminal session, issue the following command to generate the Kibana PKCS#12 certificate and private key:**

        sudo /usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12 --dns kali-purple.kali.purple,elastic.kali.purple,kali-purple --out kibana-server.p12

![](RackMultipart20231221-1-5l8g4e_html_206cd41dcba46abe.png)

*Figure 94 - DETECT: Generating Kibana PKCS#12 certificate*

1. **Enter your secure password.**

1. **In the terminal session, issue the following commands to write the client certificates (enter your secure password after each command):**

        sudo openssl pkcs12 -in /usr/share/elasticsearch/elastic-stack-ca.p12 -clcerts -nokeys -out /etc/kibana/kibana-server_ca.crt
        sudo openssl pkcs12 -in /usr/share/elasticsearch/kibana-server.p12 -out /etc/kibana/kibana-server.crt -clcerts -nokeys
        sudo openssl pkcs12 -in /usr/share/elasticsearch/kibana-server.p12 -out /etc/kibana/kibana-server.key -nocerts -nodes

![](RackMultipart20231221-1-5l8g4e_html_c23a7114e76f7ff6.png)

*Figure 95 - DETECT: Writing client certificates*

1. **In the terminal session, issue the following commands to change ownership of the new client SSL certificates to the root user, as well as modify the permissions:**

        sudo chown root:kibana /etc/kibana/kibana-server_ca.crt
        sudo chown root:kibana /etc/kibana/kibana-server.key
        sudo chown root:kibana /etc/kibana/kibana-server.crt
        sudo chmod 660 /etc/kibana/kibana-server_ca.crt
        sudo chmod 660 /etc/kibana/kibana-server.key
        sudo chmod 660 /etc/kibana/kibana-server.crt
 
 ![](RackMultipart20231221-1-5l8g4e_html_94f9ef74d477c134.png)

*Figure 96 - DETECT: Changing owner/permissions for client certs*

1. **In the terminal session, issue the following commands to modify the file /etc/kibana/kibana.yml:**

        echo "server.ssl.enabled: true" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.ssl.certificate: /etc/kibana/kibana-server.crt" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.ssl.key: /etc/kibana/kibana-server.key" | sudo tee -a /etc/kibana/kibana.yml
        echo "server.publicBaseUrl: \"https://kali-purple.kali.purple:5601\"" | sudo tee -a /etc/kibana/kibana.yml

![](RackMultipart20231221-1-5l8g4e_html_f3f15abf2df9a050.png)

*Figure 97 - DETECT: Modifying /etc/kibana/kibana.yml*

1. **In the terminal session, issue the following command to comment out the previous encryption keys entered into /etc/kibana/kibana.yml:**

        sudo sed -e '/encryptionKey/ s/^#\*/#/' -i /etc/kibana/kibana.yml

2. **In the terminal session, issue the following command to generate Kibana encryption keys and enter them into the /etc/kibana/kibana.yml file:**

        sudo /usr/share/kibana/bin/kibana-encryption-keys generate -q --force| sudo tee -a /etc/kibana/kibana.yml

![](RackMultipart20231221-1-5l8g4e_html_584ab926417df973.png)

*Figure 98 - DETECT: Generating Kibana SSL encryption keys*

1. **In the terminal session, issue the following command to restart the Kibana service:**

        sudo systemctl restart kibana

![](RackMultipart20231221-1-5l8g4e_html_794e19b7dd8513b2.png)

*Figure 99 - DETECT: Restart Kibana*

1. **In a few seconds, use a browser to navigate to https://kali-purple.kali.purple:5601. You'll be greeted by a page warning of an untrusted site (due to a self-signed certificate). Accept the risk to continue, and you will be redirected to the Kibana logon page (HTTPS).**

![](RackMultipart20231221-1-5l8g4e_html_372efc74bb5fbc4f.png)

2. **Log in with the username 'elastic' and the password you recorded from [Section 9.1.1](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/9%20-%20Configure%20DETECT%20Node.md#911-install-elasticsearch) step 3.**

![](RackMultipart20231221-1-5l8g4e_html_b8d893f00ef21333.png)

*Figure 100 - DETECT: Kibana Login*

1. **You will see the "Welcome to Elastic" page. Click "Explore on my own."**

![](RackMultipart20231221-1-5l8g4e_html_9388a19e946ab4f5.png)

2. **On the next page, in the upper right corner, click the circled "E." This will reveal a drop-down menu. Click "Edit Profile."**

![](RackMultipart20231221-1-5l8g4e_html_2a3d576789c6c2a4.png)

3. **On the "Profile" page, set a secure password for the 'elastic' superuser using the "Change password" button.**

![](RackMultipart20231221-1-5l8g4e_html_14182ca4ca0a1cb8.png)
![](RackMultipart20231221-1-5l8g4e_html_bd3ec92e61870a5.png)

*Figure 101 - DETECT: Change Elastic user password*

## 9.2 - Fleet Server

### 9.2.1 - Installation

1. **Within the Kibana interface, click the navigation button in the upper left corner, scroll down towards the bottom of the list, and click "Fleet."**

![](RackMultipart20231221-1-5l8g4e_html_c464980df9f682c5.png)

2. **On the "Fleet" page, click the "Add Fleet Server" button.**

![](RackMultipart20231221-1-5l8g4e_html_cd932a15a0a8b28e.png)

3. **On the page that appears, enter a name for your Fleet Server Policy in the "Name" field. For the URL, enter the HTTPS FQDN of your DETECT node, at port 8220. In my example, I will enter https://kali-purple.kali.purple:8220. Then, click "Generate Fleet Server policy."**

![](RackMultipart20231221-1-5l8g4e_html_71ae8a78714a7893.png)

*Figure 102 - DETECT: Fleet server policy*

1. **Once the policy has been generated, the page will provide a set of commands to paste into a terminal session for execution. Copy the command using the clipboard icon provided in the upper right corner of the command window.**

![](RackMultipart20231221-1-5l8g4e_html_9ed2c3d8074db195.png)

2. **In a terminal session, paste the command and add " --force" on the end to auto-confirm installation of the Elastic Agent. Make sure there is a space between the port 8220 and the "--force" option. If you wish, you can change the IP address to your DETECT node's FQDN for the *fleet-server-es* option.**

![](RackMultipart20231221-1-5l8g4e_html_d7cd1e51baf43d30.png)

*Figure 103 - DETECT: Fleet server installation*

1.
2. **When complete, you'll see a confirmation message in the terminal session, and the fleet server installation page will have 3 green checkmarks indicating that the fleet server has connected. DO NOT select the "Continue enrolling Elastic Agent." Simply click the small "x" in the upper right corner to close the sub-window.**

![](RackMultipart20231221-1-5l8g4e_html_55fb54d41564ea0c.png)
![](RackMultipart20231221-1-5l8g4e_html_2518495293b19d65.png)

3. **Back on the Fleet page, click the "Agent policies" tab, and select your fleet server policy.**

![](RackMultipart20231221-1-5l8g4e_html_1e0981dc3a45969f.png)

4. **On the next page, select "Add integration."**

![](RackMultipart20231221-1-5l8g4e_html_b24db3c26390218d.png)

5. **On the Integrations page, select the "Elastic Stack" category on the left side, and then "Elastic Agent" that appears on the right.**

![](RackMultipart20231221-1-5l8g4e_html_3e55b50b4c542ec.png)

6. **On the Elastic Agent screen, select "Add Elastic Agent" in the upper right.**

![](RackMultipart20231221-1-5l8g4e_html_2410556b2ef9b835.png)

7. **No changes are needed on this page. Just click the "Save and continue" button in the lower right.**

![](RackMultipart20231221-1-5l8g4e_html_42c3ce921eb8168d.png)

1. **Next, click "Save and deploy changes."**

![](RackMultipart20231221-1-5l8g4e_html_86a0a960d2b5b8b.png)

2. **Back on your fleet server policy page, you'll now see 3 integrations (elastic\_agent-1, fleet\_server-1, and system-1). Click the "Add integration" button again to add the pfSense module. This will allow the fleet server to ingest and properly parse the syslog data from the PROTECT node.**

![](RackMultipart20231221-1-5l8g4e_html_69609edb67ec3c57.png)

3. **In the search bar of the Integrations page, search "pfsense." Click the pfSense integration that appears.**

![](RackMultipart20231221-1-5l8g4e_html_d55b14a1f5e5e2d5.png)

4. **On the "Add pfSense integration" page, enter "0.0.0.0" into the "Syslog Host" field, and ensure that the port is set to 9001. Click "Save and continue" in the lower right.**

![](RackMultipart20231221-1-5l8g4e_html_668d7ec3ab467409.png)

5. **Click "Save and deploy changes."**

![](RackMultipart20231221-1-5l8g4e_html_aae61170800f1d9f.png)

6. **Now your fleet server policy has the pfSense integration added. To verify that it is ingesting data from the PROTECT node, use the navigation on the left to go to Analytics -\> Discover.**

![](RackMultipart20231221-1-5l8g4e_html_c25b1bf8b3e4a584.png)

7. **In the search bar of the Discover page, enter the following search string:**

        data_stream.dataset : "pfsense.log"

**If the DETECT node is ingesting properly, you will see data coming in.**

![](RackMultipart20231221-1-5l8g4e_html_f26dac45c6fe9f02.png)

*Figure 104 - DETECT: Kibana Discover*

### 9.2.2 - Linux Server Policy

1. **Within the Kibana interface, click the navigation button in the upper left corner, and navigate to Management -\> Fleet.**

![](RackMultipart20231221-1-5l8g4e_html_8da5d4cacd1590d3.png)

2. **On the Fleet screen, select "Agent Policies." On the right, click "Create agent policy."**

![](RackMultipart20231221-1-5l8g4e_html_27dc0497203c8445.png)

3. **For the name of the policy, enter "Linux Server Policy." Click the "Create agent policy" button in the lower right.**

![](RackMultipart20231221-1-5l8g4e_html_b29015fbf290dc14.png)

4. **On the right side of the screen, click "Add integration."**

![](RackMultipart20231221-1-5l8g4e_html_30570be593577377.png)

5. **On the Integrations page, select the "Elastic Stack" category on the left side, and then "Elastic Agent" that appears on the right.**

![](RackMultipart20231221-1-5l8g4e_html_3e55b50b4c542ec.png)

6. **On the Elastic Agent screen, select "Add Elastic Agent" in the upper right.**

![](RackMultipart20231221-1-5l8g4e_html_2410556b2ef9b835.png)

7. **Ensure the newly created Linux policy is selected for "Agent Policy." Click the "Save and continue" button in the lower right.**

![](RackMultipart20231221-1-5l8g4e_html_32018802e639c139.png)

1. **Next, click "Add Elastic Agent later."**

![](RackMultipart20231221-1-5l8g4e_html_94f0748a62cfb3e7.png)

2. **You will be returned to the "Linux Server Policy" page. There will be a window reminding you about adding Elastic Agent to your hosts. Click "Got it" to close. Your Linux policy should now show two integrations, the system (default) and the newly added Elastic Agent. We will install this agent manually on the IDENTIFY and RESPOND nodes later.**

![](RackMultipart20231221-1-5l8g4e_html_95dab02a5a69c2dd.png)

*Figure 105 - DETECT: Linux server policy*

## 9.3 - Beats

### 9.3.1 - Collect Elasticsearch Certificate Fingerprint

1. **In a terminal session, issue the following command to obtain the Elasticsearch certificate fingerprint on the DETECT node:**

        sudo openssl x509 -fingerprint -sha256 -noout -in /etc/elasticsearch/certs/http\_ca.crt | awk 'BEGIN { FS = "=" } ; { print $2 }' | sed 's/://g'

![](RackMultipart20231221-1-5l8g4e_html_c89bc3ed02ef82f5.png)

*Figure 106 - DETECT: Acquiring Elasticsearch fingerprint*

**Copy the fingerprint value as you will need it for Metricbeat and Filebeat configuration.**

### 9.3.2 - Metricbeat Installation and Configuration

1. **In a terminal session, enter the following commands to download and install Metricbeat:**

        cd ~
        curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.11.3-amd64.deb
        sudo dpkg -i metricbeat-8.11.3-amd64.deb

![](RackMultipart20231221-1-5l8g4e_html_6c79f46134327385.png)

*Figure 107 - DETECT: Installing Metricbeat*

1. **In a terminal session, issue the following command to edit the file /etc/metricbeat/metricbeat.yml:**

        sudo nano /etc/metricbeat/metricbeat.yml

1. **Find the section titled "Kibana."**

1. **Uncomment the line *#host: "localhost:5601"*. Change it to read:**

        host: "https://kali-purple.kali.purple"

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2. **Immediately below this line, add the following lines (all aligned with the previous line):**

        setup.kibana.ssl.enabled: true
        ssl.certificate\_authorities: ["/etc/kibana/kibana-server\_ca.crt"]
        setup.kibana.ssl.certificate: "/etc/kibana/kibana-server.crt"
        setup.kibana.ssl.key: "/etc/kibana/kibana-server.key"

1. **Find the section titled "Elasticsearch Output."**

1. **Uncomment the line *#hosts: ["localhost:9200"]*. Change it to read:**

        hosts: ["https://kali-purple.kali.purple"]

**\*NOTE THE ABSENCE OF THE PORT NUMBER**

2. **Uncomment the line in this section beginning with *#username*. Change it to read:**

*username: "elastic"*

3. **Uncomment the line in this section beginning with *#password*. Change it to reflect your 'elastic' user password. Ensure your password is contained within quotation marks (e.g. *password: "s3cret\_p@$$w0rd"* )**
4. **Immediately below the password line, add the following line (verify alignment):**

        ssl.ca\_trusted\_fingerprint: "<your Elasticsearch fingerprint value>"
        
![](RackMultipart20231221-1-5l8g4e_html_6bd9dd9def3cb08a.png)

Figure 108 - DETECT: Modifying /etc/metricbeat/metricbeat.yml

1. Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.
1. In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch:
**sudo metricbeat modules enable elasticsearch**![](RackMultipart20231221-1-5l8g4e_html_24c18b5a55ba6060.png)
2. In a terminal session, issue the following command to edit the file /etc/metricbeat/modules.d/elasticsearch.yml:
**sudo nano /etc/ modules.d/elasticsearch.yml**
1. Uncomment the line that begins with **#hosts** if it is commented. Change it to read **hosts: ["https://kali-purple.kali.purple:9200"]**

\*NOTE THE INCLUSION OF THE PORT NUMBER
2. Uncomment the line that begins with #username if it is commented. Change it to read username: "elastic"
3. Uncomment the line that begins with #password if it is commented. Change it to reflect your 'elastic' user's password. ![](RackMultipart20231221-1-5l8g4e_html_fdc376e60a4de6d3.png)

Figure 109 - DETECT: Modifying /etc/metricbeat/modules.d/elasticsearch.yml

1. Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.
1. In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch XPack:

**sudo metricbeat modules enable elasticsearch-xpack**![](RackMultipart20231221-1-5l8g4e_html_1515dab6687fe960.png)

1. In a terminal session, issue the following command to edit the file /etc/metricbeat/modules.d/elasticsearch-xpack.yml:

**sudo nano /etc/ modules.d/elasticsearch-xpack.yml**

1. Uncomment the line that begins with **#hosts** if it is commented. Change it to read **hosts: ["https://kali-purple.kali.purple:9200"]**

\*NOTE THE INCLUSION OF THE PORT NUMBER

1. Immediately below this line, enter the following line (ensure proper alignment):
protocol: "https"
2. Uncomment the line that begins with #username if it is commented. Change it to read username: "elastic"
3. Uncomment the line that begins with #password if it is commented. Change it to reflect your 'elastic' user's password.
4. Immediately below this line, add the following lines (ensure proper alignment, indentions are 2 spaces):
**ssl:
enabled: true
ca\_trusted\_fingerprint: " **\<your Elasticsearch fingerprint value\>**"**

**verification\_mode: "certificate"**![](RackMultipart20231221-1-5l8g4e_html_b6470672fbd630a.png)

Figure 110 - DETECT: Modifying /etc/metricbeat/modules.d/elasticsearch-xpack.yml

1. Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.
1. In a terminal session, issue the following command to test the Metricbeat configuration:
**sudo metricbeat test config**
![](RackMultipart20231221-1-5l8g4e_html_428131020627c39e.png)

Figure 111 - DETECT: Test Metricbeat configuration

1. In a terminal session, issue the following command to test the Metricbeat modules:
**sudo metricbeat test modules**
![](RackMultipart20231221-1-5l8g4e_html_5075a45560c9d3ab.png)

Figure 112 - DETECT: Test Metricbeat modules


**NOTE** : The first 7 or so lines may show errors upon execution. This will not affect the success of the installation. This step may take a few seconds to complete.
2. In a terminal session, issue the following command to set up Metricbeat index patterns and load dashboards into Kibana: **sudo metricbeat setup**
![](RackMultipart20231221-1-5l8g4e_html_1e9f7319362e2eff.png)

Figure 113 - DETECT: Metricbeat setup


This will take a few minutes to complete.
2. In the terminal session, issue the following command to configure Metricbeat for automatic startup: **sudo systemctl enable metricbeat –now**![](RackMultipart20231221-1-5l8g4e_html_2add400f5da2a40.png)
3. In Kibana (in a browser), use the navigation on the left to go to Management -\> Stack Monitoring.
![](RackMultipart20231221-1-5l8g4e_html_65ebdb74be7abe2a.png)
4. On the next screen, select "Set up monitoring with Metricbeat." ![](RackMultipart20231221-1-5l8g4e_html_d521e11272593771.png)
5. Click "Monitor with Metricbeat" below the DETECT node's name. ![](RackMultipart20231221-1-5l8g4e_html_34fdc79496c85b49.png)
6. Click "Next."
7. Ensure "Yes" is selected on the "Create rules" window, and click "Continue." ![](RackMultipart20231221-1-5l8g4e_html_a97ac19e270603.png)
8. Click "Exit setup mode" at either the top or the bottom of the window on the right side.

### 9.3.3 - Filebeat Installation and Configuration

      1. In a terminal session, enter the following commands to download and install Filebeat:
**cd ~**

**curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.11.3-amd64.deb**

**sudo dpkg -i filebeat-8.11.3-amd64.deb**![](RackMultipart20231221-1-5l8g4e_html_1c167204c5fd4520.png)

Figure 114 - DETECT: Install Filebeat

      1. In a terminal session, issue the following command to edit the file /etc/filebeat/filebeat.yml:
**sudo nano /etc/filebeat/filebeat.yml**
        1. Find the section titled "Kibana."
          1. Uncomment the line **#host: "localhost:5601"**. Change it to read
**host: "https://kali-purple.kali.purple"**

\*NOTE THE ABSENCE OF THE PORT NUMBER
          2. Immediately below this line, add the following lines (all aligned with the previous line):
**setup.kibana.ssl.enabled: true
 ssl.certificate\_authorities: ["/etc/kibana/kibana-server\_ca.crt"]**

**setup.kibana.ssl.certificate: "/etc/kibana/kibana-server.crt"**

**setup.kibana.ssl.key: "/etc/kibana/kibana-server.key"**

        1. Find the section titled "Elasticsearch Output."
          1. Uncomment the line **#hosts: ["localhost:9200"]**. Change it to read
**hosts: ["https://kali-purple.kali.purple"]**

\*NOTE THE ABSENCE OF THE PORT NUMBER
          2. Uncomment the line in this section beginning with **#username**. Change it to read **username: "elastic"**
          3. Uncomment the line in this section beginning with **#password**. Change it to reflect your 'elastic' user password. Ensure your password is contained within quotation marks (e.g. **password: "s3cret\_p@$$w0rd"** )
          4. Immediately below the password line, add the following line (verify alignment):
**ssl.ca\_trusted\_fingerprint: "** \<your Elasticsearch fingerprint value\> **"**![](RackMultipart20231221-1-5l8g4e_html_94dc7db45cb230fa.png)

Figure 115 - DETECT: Modify /etc/filebeat/filebeat.yml

        1. Save the document using CTRL+X, Y, then ENTER to exit the Nano editor.
      1. In a terminal session, issue the following command to enable the Metricbeat modules for Elasticsearch:
**sudo filebeat modules enable elasticsearch**![](RackMultipart20231221-1-5l8g4e_html_f83ee35bfe7406dc.png)
      2. In a terminal session, issue the following command to edit the file /etc/filebeat/modules.d/elasticsearch.yml:
**sudo sed -I 's/false/true/g' /etc/filebeat/modules.d/elasticsearch.yml**![](RackMultipart20231221-1-5l8g4e_html_a6e0079c98ee612c.png)

Figure 116 - DETECT: Modify /etc/filebeat/modules.d/elasticsearch.yml

      1. In a terminal session, issue the following command to set up Filebeat index patterns and load dashboards into Kibana: **sudo filebeat setup**
 ![](RackMultipart20231221-1-5l8g4e_html_54765bd485f9e6a1.png)

Figure 117 - DETECT: Filebeat setup


 This will take a few minutes to complete.
      2. In the terminal session, issue the following command to configure Filebeat for automatic startup: **sudo systemctl enable filebeat –now**![](RackMultipart20231221-1-5l8g4e_html_8ebf502dab4ba6a7.png)
      3. In Kibana (in a browser), use the navigation on the left to go to Management -\> Dev Tools.
 ![](RackMultipart20231221-1-5l8g4e_html_65e5c0a37cfe1923.png)
      4. Delete the data in the left pane and enter the following:
**PUT \_settings
 {
 "number\_of\_replicas": 0
 }

 NOTE 1:** There is a space between "PUT" and "\_settings"
**NOTE 2** : Line 3 is indented 2 spaces, and ends with the number 0.
**NOTE 3** : The last line with the "}" has no empty space/line after it.
      5. Press the green triangle on the right side of the left pane to execute the command. ![](RackMultipart20231221-1-5l8g4e_html_26e8e4f44485c8d9.png)
      6. Upon successful execution, the right pane will have a green "200 OK" above it, with the following message in the pane:
**{
 "acknowledged": true
 }**![](RackMultipart20231221-1-5l8g4e_html_4e0b34900d665d41.png)

---
<div align="center">
| [Previous Section: 8 - Kali Purple](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/8%20-%20Kali%20Purple.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 10 - Configure: IDENTIFY Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/10%20-%20Configure%20IDENTIFY%20Node.md) |
</div>