# 10 - Configure: IDENTIFY Node

<details>
<summary><h2>10.1 - Elastic Agent</h2></summary>

### 10.1.1 - Installation

1. **In a terminal session, issue the following command to install rSyslog:**

        sudo apt install rsyslog -y

 ![](RackMultipart20231221-1-5l8g4e_html_9e022bf861239bd6.png)

*Figure 118 - IDENTIFY: Install rSyslog*

2. **In a browser, browse to the Fleet page Elastic SIEM on your DETECT node's FQDN on port 5601 via HTTPS (e.g. https://kali-purple.kali.purple:5601/app/fleet/policies). Log in with your 'elastic' account credentials.**

![](RackMultipart20231221-1-5l8g4e_html_8c7028f60c4c1203.png)

3. **Click the "Agent policies" tab and select your Linux server policy created in section 9.2.2.**

![](RackMultipart20231221-1-5l8g4e_html_ce0f153e3809ed39.png)

4. **In the upper section of the Linux server policy page, click "Add agent."**

![](RackMultipart20231221-1-5l8g4e_html_d3593bb21722c05e.png)

5. **On the "Add agent" screen, scroll down to step 3 (Install Elastic Agent on your host). Ensure that the Linux Tar" option is selected, and copy the contents of the command box (use the clipboard icon as a copy shortcut).**

![](RackMultipart20231221-1-5l8g4e_html_dbf2f2c8f4baadf0.png)

6. **In a terminal session, paste the commands into the terminal. Ensure to add " --insecure --force" at the end. There is a space between the enrolment-token and the "--insecure", as well as between the "--insecure" and "--force" options. Hit ENTER to install Elastic Agent.**

![](RackMultipart20231221-1-5l8g4e_html_e85082b9f9b83adf.png)

*Figure 119 - IDENTIFY: Elastic Agent install*

7. **When installation has been completed, the browser page will indicate that the agent enrolment and incoming data has been confirmed.**

![](RackMultipart20231221-1-5l8g4e_html_6eeb49d6895a8457.png)

*Figure 120 - IDENTIFY: Elastic Agent enrolled in Fleet*

8. **To confirm that you are seeing data from this host:**

    1. **Navigate to Analytics -> Dashboard.**

    ![](RackMultipart20231221-1-5l8g4e_html_1435318890e5ffeb.png)

    2. **Search "System Overview" and select the dashboard titled "[Metrics System] Overview."**

    ![](RackMultipart20231221-1-5l8g4e_html_4f5017d636e166ba.png)

    3. **Your host will be listed in the "Host" tile.**
    
    ![](RackMultipart20231221-1-5l8g4e_html_8b7478135a23884b.png)
</details>

<details>
<summary><h2>10.2 - Docker</h2></summary>

### 10.2.1 - Installation

1. **In a terminal session, issue the following command to install Docker:**

        sudo apt install docker.io -y

![](RackMultipart20231221-1-5l8g4e_html_22841e681769929e.png)

*Figure 121 - DOCKER: Installation*

1. **In a terminal session, issue the following command to configure automatic startup for the Docker service:**

        sudo systemctl enable docker –now

![](RackMultipart20231221-1-5l8g4e_html_944a10d84c53d689.png)

2. **In a terminal session, issue the following commands to allow for non-root Docker management:**

        sudo usermod -aG docker $USER
        sudo chmod 666 /var/run/docker.sock

![](RackMultipart20231221-1-5l8g4e_html_b00b6b03788b1fea.png)

### 10.2.2 - Portainer

1. **In a terminal session, issue the following commands to create a Docker volume for and install Portainer:**

        docker volume create portainer_data
        docker run -d -p 18000:18000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

![](RackMultipart20231221-1-5l8g4e_html_6494caca5044cae1.png)

*Figure 122 - DOCKER: Install Portainer*

1. **In a terminal session, issue the following command to verify that Portainer is running:**

        docker ps

![](RackMultipart20231221-1-5l8g4e_html_1262ad38556162cc.png)

2. **In a browser, browse to your IDENTIFY node's FQDN on port 9443 (e.g. https://kali-violet.kali.purple:9443 ). Accept the security risk to continue to the login page**

![](RackMultipart20231221-1-5l8g4e_html_c0504ff58dd8ca59.png)

3. **Create and confirm a secure password for your Portainer 'admin' account. Clear the checkmark in the box to disable collection of anonymous statistics. Click "Create user" when done.**

![](RackMultipart20231221-1-5l8g4e_html_3c857d3d8c5d84ff.png)

4. **On the "Quick Setup" page, click "Home" in the upper left.**

![](RackMultipart20231221-1-5l8g4e_html_57c37c5f4990083e.png)

5. **On the home page, click your local environment. This will bring you to your dashboard.**

![](RackMultipart20231221-1-5l8g4e_html_cafdfd1ca5601c46.png)
</details>

<details>
<summary><h2>10.3 - Greenbone Vulnerability Management (GVM)</h2></summary>

### 10.3.1 - Installation and configuration

1. **In a terminal session, issue the following command to install the GVM platform:**

        sudo apt install gvm -y

![](RackMultipart20231221-1-5l8g4e_html_85625b552cc269b3.png)

*Figure 123 - IDENTIFY: Install Greenbone Vulnerability Management*

1. **In a terminal session, issue the following command to initialize the GVM setup:**

        sudo gvm-setup

![](RackMultipart20231221-1-5l8g4e_html_cd79417e45927314.png)

*Figure 124 - IDENTIFY: GVM Setup*

**NOTE: This will take several minutes to complete. When it finishes, take note of the administrator password that will be displayed.**

2. **In a terminal session, issue the following command to check the GVM configuration:**

        sudo gvm-check-setup

![](RackMultipart20231221-1-5l8g4e_html_12628b1e944ec292.png)

3. **In a terminal session, issue the following commands to make GVM available on the external interface:**

        sudo sed -e 's/127.0.0.1/0.0.0.0/g' -i /lib/systemd/system/gsad.service
        sudo systemctl daemon-reload
        
![](RackMultipart20231221-1-5l8g4e_html_17430b09f970a33c.png)

1. **In a terminal session, issue the following commands to restart GVM:**

        sudo gvm-stop

![](RackMultipart20231221-1-5l8g4e_html_ba1c991b381555e2.png)

        sudo gvm-start

![](RackMultipart20231221-1-5l8g4e_html_b6bd14befbaf122d.png)

**NOTE : When GVM starts up, it will try to automatically launch a browser (Firefox) to the GVM console ([https://127.0.0.1:9392/login).](https://127.0.0.1:9392/login).**

![](RackMultipart20231221-1-5l8g4e_html_5ec746677a7561c6.png)

2. **In the open browser, login with the username 'admin' and the password recorded from step 2.**

![](RackMultipart20231221-1-5l8g4e_html_bb753b6f9585a134.png)

*Figure 125 - IDENTIFY: GVM Login portal*

1. **On the main page after login, click the user icon in the upper right, and select "My settings" from the drop-down menu.**

![](RackMultipart20231221-1-5l8g4e_html_c48c4404f35ba391.png)

2. **On the "My Settings" page, select the edit icon (looks like a sheet of paper with a pen) to edit the user settings.**

![](RackMultipart20231221-1-5l8g4e_html_4ee6be43a5c792cf.png)

3. **Set a new secure password for the 'admin' user. You can adjust any other settings you wish here. Click "Save" when done.**

![](RackMultipart20231221-1-5l8g4e_html_7b63d6d816c25df3.png)
</details>

<details>
<summary><h2>10.4 - OpenCTI</h2></summary>

**At the time of the drafting of the Proxmox instructions, the OpenCTI version that was available was 5.5.2. The latest version as of the drafting of these instructions is 5.12.9, however I was running into issues with getting that version to deploy. The instructions to follow will refer to the most recent version I could successfully deploy: v5.12.8.**

### 10.4.1 - Installation

1. **On your DETECT node, open a browser and browse to your IDENTIFY node's Portainer portal via HTTPS on port 9443 (e.g. https://kali-violet.kali.purple:9443). Log into Portainer using your admin credentials.**

![](RackMultipart20231221-1-5l8g4e_html_23e719f58c7d6367.png)

*Figure 126 - IDENTIFY: Portainer Login Portal*

1. **Click on the local environment in the center (the Docker icon). Then on the left side of the next page int the navigation area, select "Stacks."**

![](RackMultipart20231221-1-5l8g4e_html_165cb8905874bd0c.png)

2. **In the upper right, click "Add stack."**

![](RackMultipart20231221-1-5l8g4e_html_33fdc62278997a3c.png)

3. **In the name field, enter "opencti." Ensure the "Web editor" option is selected for the build method.**

4. **In another browser (or tab), browse to the OpenCTI Github repository at https:/github.com/OpenCTI-Platform/docker/blob/master/docker-compose.yml. Use the copy icon to copy all the raw text from the file to your clipboard. You can close this tab/window as we no longer need it.**

![](RackMultipart20231221-1-5l8g4e_html_573c72eeffeef01e.png)

*Figure 127 - IDENTIFY: OpenCTI docker-compose.yml file*

1. **Back on the Portainer tab/window, paste the data into the Web editor pane. In this pane, change all occurrences of "5.12.9" to "5.12.8" (there are 7). Then, under the Environmental variables section, click "Advanced mode." Keep this window/tab open, as we will return to it in a bit.**

![](RackMultipart20231221-1-5l8g4e_html_f47f4114b656797f.png)

*Figure 128 - IDENTIFY: Portainer Stack Web Editor*

1. **In a terminal session, issue the following commands to create and enter the /opt/opencti folder:**

        sudo mkdir /opt/opencti
        cd /opt/opencti

![](RackMultipart20231221-1-5l8g4e_html_eed18c0680ea8d7e.png)

2. **Issue the following commands to create the environmental variables file /opt/opencti/.env:**

        sudo bash -c "(cat << EOF
        OPENCTI_ADMIN_EMAIL=your@email.address
        OPENCTI_BASE_URL=http://your-IDENTIFY-node-FQDN:8080
        OPENCTI_ADMIN_PASSWORD=your-OpenCTI-Admin-password
        OPENCTI_ADMIN_TOKEN=$(cat /proc/sys/kernel/random/uuid)
        MINIO_ROOT_USER=$(cat /proc/sys/kernel/random/uuid)
        MINIO_ROOT_PASSWORD=$(cat /proc/sys/kernel/random/uuid)
        RABBITMQ_DEFAULT_USER=opencti
        RABBITMQ_DEFAULT_PASS=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_HISTORY_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_EXPORT_FILE_STIX_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_EXPORT_FILE_CSV_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_EXPORT_FILE_TXT_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_IMPORT_DOCUMENT_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_IMPORT_FILE_STIX_ID=$(cat /proc/sys/kernel/random/uuid)
        CONNECTOR_IMPORT_REPORT_ID=$(cat /proc/sys/kernel/random/uuid)
        SMTP_HOSTNAME=localhost
        ELASTIC_MEMORY_SIZE=4G
        OPENCTI_KEY_PATH=/etc/ssl/localcerts/server.key
        OPENCTI_CRT_PATH=/etc/ssl/localcerts/server.cert
        CONNECTOR_OPENCTI_ID=$(cat /proc/sys/kernel/random/uuid)
        EOF
        ) > .env"

**NOTE : Set your own values for OPENCTI_ADMIN_EMAIL, OPENCTI_ADMIN_PASSWORD, and insert your IDENTIFY nodes FQDN into the OPENCTI_BASE_URL variable value.**

 ![](RackMultipart20231221-1-5l8g4e_html_a123923fdf3eb634.png)

1. **Issue the following command to modify the permissions of the environmental variables file /opt/opencti/.env:**

        sudo chmod 600 .env

2. **Issue the following command to display the contents of the environmental variables file:**

        sudo cat .env

![](RackMultipart20231221-1-5l8g4e_html_a162c4d0673def87.png)

3. **Copy the contents of the environmental variables file into the environmental variables pane of the browser window (opencti Portainer stack). Ensure the variables such as your email address, base URL and password have been set.**

![](RackMultipart20231221-1-5l8g4e_html_636328e92adaa78.png)

*Figure 129 - IDENTIFY: Portainer Stack Environmental Variables*

1. **Near the bottom of the screen, click "Deploy the stack."**

![](RackMultipart20231221-1-5l8g4e_html_f12963bbfd0126d3.png)

2. **The deployment process will take some time, as Docker is pulling 11 images down in the background in support of the OpenCTI platform.**

![](RackMultipart20231221-1-5l8g4e_html_389f48b71d544e50.png)

3. **Once the deployment is complete, navigate to the "Stacks" page if you're not already there.**

![](RackMultipart20231221-1-5l8g4e_html_b0b919576ca24da7.png)

4. **Now you have a deployment stack named "opencti." Click on the stack to view the details.**

![](RackMultipart20231221-1-5l8g4e_html_1876fb6e5fa0a203.png)

*Figure 130 - IDENTIFY: OpenCTI Portainer Stack*

1. **On the "Stack details" screen, you can see all containers that are a part of the stack. Keep this window/tab open as we will return in a bit.**

### Enable HTTPS

1. **In a terminal session, issue the following commands to generate new certificates for the OpenCTI instance:**
        
        sudo openssl req -nodes -new -x509 -keyout server.key -out server .cert
        sudo mv server.* /etc/ssl/localcerts

    1. Enter the 2-letter code for your country (e.g. US)
    2. Enter the full name of your state (e.g. Colorado)
    3. (OPTIONAL) Enter the name of your city
    4. Enter the name of your company (e.g. CDX Cyber Security Operations Center)
    5. (OPTIONAL) Enter your organizational unit name
    6. (OPTIONAL) Enter a Common Name
    7. (OPTIONAL) Enter an email address

 ![](RackMultipart20231221-1-5l8g4e_html_823510cf88f1c061.png)

*Figure 131 - IDENTIFY: OpenCTI SSL Certificate Generation*

2. **Returning to the browser, on the "Stack details" page, click the "Editor" option.**

 ![](RackMultipart20231221-1-5l8g4e_html_6d42e786ce0348c1.png)

3. **In the editor window, find the line that reads "- APP__PORT=8080" (this should be around line 52). Immediately after this line, insert the following (mind the alignment):**

        - APP__HTTPS_CERT__KEY=${OPENCTI_KEY_PATH}
        - APP__HTTPS_CERT__CRT=${OPENCTI_CRT_PATH}
        - APP__HTTPS_CERT__REJECT_UNAUTHORIZED=false

**NOTE: These entries should be aligned with entries above and below them.**

 ![](RackMultipart20231221-1-5l8g4e_html_d72ee4883fef6e5d.png)

4. **In the editor window, find the line that reads "- "8080:8080" (this should be around line 79). Immediately after this line, insert the following (mind the alignment):**

        volumes:
          - /etc/ssl/localcerts/:/etc/ssl/localcerts:ro**

**NOTE : The first line ("volumes:") should be aligned with "ports:" above it. The second line should be indented 2 spaces further to the right than "volumes:".**

 ![](RackMultipart20231221-1-5l8g4e_html_f86738b6648847fe.png)

5. **In the editor window, change all occurrences of "http://opencti:8080" to "https://opencti:8080" (there are 6).**

![](RackMultipart20231221-1-5l8g4e_html_153ffaa5de5bba3b.png)

6. **In the Environmental variables section of the stack, add two new environmental variables:**

    1. NAME: *OPENCTI_KEY_PATH* , VALUE: */etc/ssl/localcerts/server.key*
    2. NAME: *OPENCTI_CRT_PATH* , VALUE: */etc/ssl/localcerts/server.cert*

![](RackMultipart20231221-1-5l8g4e_html_b9d8c08b1c3db3aa.png)

7. **Near the bottom of the page, click "Update the stack."**

![](RackMultipart20231221-1-5l8g4e_html_c16a34b36bce2a91.png)

8. **Click "Update" to confirm stack refresh.**

![](RackMultipart20231221-1-5l8g4e_html_bf4ac8f23754e3f1.png)

9. **When the refresh is complete, use another tab in your browser to log into OpenCTI. The URL will be the FQDN of your IDENTIFY node using HTTPS on port 8080 (e.g. https://kali-violet.kali.purple:8080). Your credentials will be the email address and password you provided in the stack configuration.**

![](RackMultipart20231221-1-5l8g4e_html_3a1343c9ded3569.png)

*Figure 132 - IDENTIFY: OpenCTI Login Portal*

![](RackMultipart20231221-1-5l8g4e_html_224f60f8840f7a7b.png)

*Figure 133 - IDENTIFY: OpenCTI Dashboard (unpopulated)*

### Add Connectors

1. **In a terminal session, navigate to the /opt/opencti folder and issue the following commands to generate an OpenCTI Connector ID:**

        sudo bash -c "(cat << EOF
        CONNECTOR_OPENCTI_ID=$(cat /proc/sys/kernel/random/uuid)
        EOF
        ) >> .env"

2. **Issue the following command to display the new UUID generated in the environmental variables file:**

        sudo cat .env | grep CONNECTOR_OPENCTI_ID

**Copy the output to your clipboard.**

![](RackMultipart20231221-1-5l8g4e_html_7a91f2e6ba0d7a59.png)

2. **Back in the Portainer tab of your browser, return to the "Stack details" page for your OpenCTI stack. Edit the stack.**

3. **In the Environmental variables section, click on "Advanced mode," and copy the new UUID entry to the end of the list. Then click "Update the stack."**

![](RackMultipart20231221-1-5l8g4e_html_f3fc61609a224ef5.png)

*Figure 134 - IDENTIFY: OpenCTI Dashboard (populated)*
</details>

---
<div align="center">
| [Previous Section: 9 - Configure: DETECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/9%20-%20Configure%20DETECT%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/README.md) | [Next Section: 11 - Configure: RESPOND Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/edit_1/_documentation/11%20-%20Configure%20RESPOND%20Node.md) |
</div>