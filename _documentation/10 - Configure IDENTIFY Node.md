# 10 - Configure: IDENTIFY Node

<details>
<summary><h2>10.1 - Elastic Agent</h2></summary>

### 10.1.1 - Installation

1. **In a terminal session, issue the following command to install rSyslog:**

        sudo apt install rsyslog -y

 ![](_images/118._rSyslog_Install.PNG)

*Figure 118 - IDENTIFY: Install rSyslog*

2. **In a browser, browse to the Fleet page Elastic SIEM on your DETECT node's FQDN on port 5601 via HTTPS (e.g. https://kali-purple.kali.purple:5601/app/fleet/policies). Log in with your 'elastic' account credentials.**

![](_images/118a._Log_into_Elastic.PNG)

3. **Click the "Agent policies" tab and select your Linux server policy created in section 9.2.2.**

![](_images/118b._Select_Linux_Policy.PNG)

4. **In the upper section of the Linux server policy page, click "Add agent."**

![](_images/118c._Add_Agent.PNG)

5. **On the "Add agent" screen, scroll down to step 3 (Install Elastic Agent on your host). Ensure that the Linux Tar" option is selected, and copy the contents of the command box (use the clipboard icon as a copy shortcut).**

![](_images/118d._Copy_Install_Agent_command.PNG)

6. **In a terminal session, paste the commands into the terminal. Ensure to add " --insecure --force" at the end. There is a space between the enrolment-token and the "--insecure", as well as between the "--insecure" and "--force" options. Hit ENTER to install Elastic Agent.**

![](_images/119._Install_Agent.PNG)

*Figure 119 - IDENTIFY: Elastic Agent install*

7. **When installation has been completed, the browser page will indicate that the agent enrolment and incoming data has been confirmed.**

![](_images/120._Successful_Install_of_Agent.PNG)

*Figure 120 - IDENTIFY: Elastic Agent enrolled in Fleet*

8. **To confirm that you are seeing data from this host:**

    1. **Navigate to Analytics -> Dashboard.**

    ![](_images/120a._Verify_Agent.PNG)

    2. **Search "System Overview" and select the dashboard titled "[Metrics System] Overview."**

    ![](_images/120b._Verify_Agent.PNG)

    3. **Your host will be listed in the "Host" tile.**
    
    ![](_images/120c._Verify_Agent_(RESPOND).PNG)
</details>

<details>
<summary><h2>10.2 - Docker</h2></summary>

### 10.2.1 - Installation

1. **In a terminal session, issue the following command to install Docker:**

        sudo apt install docker.io -y

![](_images/121._Install_Docker.PNG)

*Figure 121 - DOCKER: Installation*

1. **In a terminal session, issue the following command to configure automatic startup for the Docker service:**

        sudo systemctl enable docker –now

![](_images/121a._Enable_Docker.PNG)

2. **In a terminal session, issue the following commands to allow for non-root Docker management:**

        sudo usermod -aG docker $USER
        sudo chmod 666 /var/run/docker.sock

![](_images/121b._Manage_Docker_as_non-root.PNG)

### 10.2.2 - Portainer

1. **In a terminal session, issue the following commands to create a Docker volume for and install Portainer:**

        docker volume create portainer_data
        docker run -d -p 18000:18000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

![](_images/122._Install_Portainer.PNG)

*Figure 122 - DOCKER: Install Portainer*

1. **In a terminal session, issue the following command to verify that Portainer is running:**

        docker ps

![](_images/122a._Check_for_Portainer_container.PNG)

2. **In a browser, browse to your IDENTIFY node's FQDN on port 9443 (e.g. https://kali-violet.kali.purple:9443 ). Accept the security risk to continue to the login page**

![](_images/122b._Portainer_URL.PNG)

3. **Create and confirm a secure password for your Portainer 'admin' account. Clear the checkmark in the box to disable collection of anonymous statistics. Click "Create user" when done.**

![](_images/122c._Portainer_Admin_Password.PNG)

4. **On the "Quick Setup" page, click "Home" in the upper left.**

![](_images/122d._Portainer_Landing_Page.PNG)

5. **On the home page, click your local environment. This will bring you to your dashboard.**

![](_images/122e._Portainer_-_Local_Connection.PNG)
</details>

<details>
<summary><h2>10.3 - Greenbone Vulnerability Management (GVM)</h2></summary>

### 10.3.1 - Installation and configuration

1. **In a terminal session, issue the following command to install the GVM platform:**

        sudo apt install gvm -y

![](_images/123._Installation.PNG)

*Figure 123 - IDENTIFY: Install Greenbone Vulnerability Management*

1. **In a terminal session, issue the following command to initialize the GVM setup:**

        sudo gvm-setup

![](_images/124._Setup.PNG)

*Figure 124 - IDENTIFY: GVM Setup*

**NOTE: This will take several minutes to complete. When it finishes, take note of the administrator password that will be displayed.**

2. **In a terminal session, issue the following command to check the GVM configuration:**

        sudo gvm-check-setup

![](_images/124a._Check_Setup.PNG)

3. **In a terminal session, issue the following commands to make GVM available on the external interface:**

        sudo sed -e 's/127.0.0.1/0.0.0.0/g' -i /lib/systemd/system/gsad.service
        sudo systemctl daemon-reload
        
![](_images/124b._Modify_gsad.service.PNG)

1. **In a terminal session, issue the following commands to restart GVM:**

        sudo gvm-stop

![](_images/124c._Stop_GVM.PNG)

        sudo gvm-start

![](_images/124d._Start_GVM.PNG)

**NOTE : When GVM starts up, it will try to automatically launch a browser (Firefox) to the GVM console ([https://127.0.0.1:9392/login).](https://127.0.0.1:9392/login).**

![](_images/124e._Start_GVM.PNG)

2. **In the open browser, login with the username 'admin' and the password recorded from step 2.**

![](_images/125._GVM_Login_Portal.PNG)

*Figure 125 - IDENTIFY: GVM Login portal*

1. **On the main page after login, click the user icon in the upper right, and select "My settings" from the drop-down menu.**

![](_images/125a._Change_Password.PNG)

2. **On the "My Settings" page, select the edit icon (looks like a sheet of paper with a pen) to edit the user settings.**

![](_images/125b._Change_Password.PNG)

3. **Set a new secure password for the 'admin' user. You can adjust any other settings you wish here. Click "Save" when done.**

![](_images/125c._Change_Password.PNG)
</details>

<details>
<summary><h2>10.4 - OpenCTI</h2></summary>

**At the time of the drafting of the Proxmox instructions, the OpenCTI version that was available was 5.5.2. The latest version as of the drafting of these instructions is 5.12.9, however I was running into issues with getting that version to deploy. The instructions to follow will refer to the most recent version I could successfully deploy: v5.12.8.**

### 10.4.1 - Installation

1. **On your DETECT node, open a browser and browse to your IDENTIFY node's Portainer portal via HTTPS on port 9443 (e.g. https://kali-violet.kali.purple:9443). Log into Portainer using your admin credentials.**

![](_images/126._Log_into_Portainer.png)

*Figure 126 - IDENTIFY: Portainer Login Portal*

1. **Click on the local environment in the center (the Docker icon). Then on the left side of the next page int the navigation area, select "Stacks."**

![](_images/139a._Select_Stacks.png)

2. **In the upper right, click "Add stack."**

![](_images/126b._OpenCTI_-_Add_a_Stack.PNG)

3. **In the name field, enter "opencti." Ensure the "Web editor" option is selected for the build method.**

4. **In another browser (or tab), browse to the OpenCTI Github repository at https:/github.com/OpenCTI-Platform/docker/blob/master/docker-compose.yml. Use the copy icon to copy all the raw text from the file to your clipboard. You can close this tab/window as we no longer need it.**

![](_images/127._OpenCTI_-_Copy_OpenCTI_docker-compose-yml.PNG)

*Figure 127 - IDENTIFY: OpenCTI docker-compose.yml file*

1. **Back on the Portainer tab/window, paste the data into the Web editor pane. In this pane, change all occurrences of "5.12.9" to "5.12.8" (there are 7). Then, under the Environmental variables section, click "Advanced mode." Keep this window/tab open, as we will return to it in a bit.**

![](_images/128._OpenCTI_-_Create_Stack_1.PNG)

*Figure 128 - IDENTIFY: Portainer Stack Web Editor*

1. **In a terminal session, issue the following commands to create and enter the /opt/opencti folder:**

        sudo mkdir /opt/opencti
        cd /opt/opencti

![](_images/128a._OpenCTI_-_Create_ENV_folder.PNG)

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

 ![](_images/128b._OpenCTI_-_Generate_ENV_file.PNG)

1. **Issue the following command to modify the permissions of the environmental variables file /opt/opencti/.env:**

        sudo chmod 600 .env

2. **Issue the following command to display the contents of the environmental variables file:**

        sudo cat .env

![](_images/128c._OpenCTI_-_Display_contents_of_ENV_file.PNG)

3. **Copy the contents of the environmental variables file into the environmental variables pane of the browser window (opencti Portainer stack). Ensure the variables such as your email address, base URL and password have been set.**

![](_images/129._OpenCTI_-_Paste_contents_of_ENV_file_to_stack.PNG)

*Figure 129 - IDENTIFY: Portainer Stack Environmental Variables*

1. **Near the bottom of the screen, click "Deploy the stack."**

![](_images/129a._OpenCTI_-_Deploy_stack.PNG)

2. **The deployment process will take some time, as Docker is pulling 11 images down in the background in support of the OpenCTI platform.**

![](_images/129b._OpenCTI_-_Deployment_in_progress.PNG)

3. **Once the deployment is complete, navigate to the "Stacks" page if you're not already there.**

![](_images/129c._OpenCTI_-_opencti_stack.PNG)

4. **Now you have a deployment stack named "opencti." Click on the stack to view the details.**

![](_images/130._OpenCTI_-_opencti_stack_details.PNG)

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

 ![](_images/131._OpenCTI_-_SSL_Certs.PNG)

*Figure 131 - IDENTIFY: OpenCTI SSL Certificate Generation*

2. **Returning to the browser, on the "Stack details" page, click the "Editor" option.**

 ![](_images/131a._OpenCTI_-_Edit_Stack.PNG)

3. **In the editor window, find the line that reads "*- APP__PORT=8080*" (this should be around line 52). Immediately after this line, insert the following (mind the alignment):**

        - APP__HTTPS_CERT__KEY=${OPENCTI_KEY_PATH}
        - APP__HTTPS_CERT__CRT=${OPENCTI_CRT_PATH}
        - APP__HTTPS_CERT__REJECT_UNAUTHORIZED=false

**NOTE: These entries should be aligned with entries above and below them.**

 ![](_images/131b._OpenCTI_-_Insert_SSL_data_into_Stack.PNG)

4. **In the editor window, find the line that reads "*- "8080:8080"*" (this should be around line 79). Immediately after this line, insert the following (mind the alignment):**

        volumes:
          - /etc/ssl/localcerts/:/etc/ssl/localcerts:ro**

**NOTE : The first line ("volumes:") should be aligned with "ports:" above it. The second line should be indented 2 spaces further to the right than "volumes:".**

 ![](_images/131c._OpenCTI_-_Add_OpenCTI_volume_config_into_Stack.PNG)

5. **In the editor window, change all occurrences of "http://opencti:8080" to "https://opencti:8080" (there are 6).**

![](_images/131d._OpenCTI_-_HTTP_to_HTTPS.PNG)

6. **In the Environmental variables section of the stack, add two new environmental variables:**

    1. NAME: *OPENCTI_KEY_PATH* , VALUE: */etc/ssl/localcerts/server.key*
    2. NAME: *OPENCTI_CRT_PATH* , VALUE: */etc/ssl/localcerts/server.cert*

![](_images/131e._OpenCTI_-_New_ENV_vars_-_2.PNG)

7. **Near the bottom of the page, click "Update the stack."**

![](_images/131f._OpenCTI_-_Update_Stack.PNG)

8. **Click "Update" to confirm stack refresh.**

![](_images/131g._OpenCTI_-_Confirm_Update_Stack.PNG)

9. **When the refresh is complete, use another tab in your browser to log into OpenCTI. The URL will be the FQDN of your IDENTIFY node using HTTPS on port 8080 (e.g. https://kali-violet.kali.purple:8080). Your credentials will be the email address and password you provided in the stack configuration.**

![](_images/132._HTTPS_OpenCTI.PNG)

*Figure 132 - IDENTIFY: OpenCTI Login Portal*

![](_images/133._Empty_OpenCTI_dashboard.PNG)

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

![](_images/133a._Connector_ID.png)

2. **Back in the Portainer tab of your browser, return to the "Stack details" page for your OpenCTI stack. Edit the stack.**

3. **In the Environmental variables section, click on "Advanced mode," and copy the new UUID entry to the end of the list. Then click "Update the stack."**

![](_images/134._Populated_OpenCTI_dashboard.PNG)

*Figure 134 - IDENTIFY: OpenCTI Dashboard (populated)*
</details>

---
<div align="center">
| [Previous Section: 9 - Configure: DETECT Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/9%20-%20Configure%20DETECT%20Node.md) | [Table of Contents](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/README.md) | [Next Section: 11 - Configure: RESPOND Node](https://gitlab.com/phybroptyx/vsphere-kali-purple-soc-in-a-box/-/blob/main/_documentation/11%20-%20Configure%20RESPOND%20Node.md) |
</div>