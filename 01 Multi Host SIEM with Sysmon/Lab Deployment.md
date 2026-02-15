# SIEM Deployment & Log Ingestion
---

## 1. Objective

Provision an Ubuntu server and deploy Wazuh to establish a centralized SIEM, then onboard an existing Windows 10 lab endpoint with Sysmon configured to forward telemetry for multi-host detection testing. This documents the deployment of the SIEM infrastructure only. Detection scenarios and investigations built on this environment are documented separately.

---

## 2. Environment
- Ubuntu Server VM (base install)
- Wazuh manager and dashboard installed via CLI
- Windows 10 endpoint (preconfigured in prior lab project [01-Windows-VM-Setup-and-Baseline](https://github.com/miholmestech/SOC-Analyst-Projects-/tree/main/01-Windows-VM-Setup-and-Baseline))
- Sysmon configured for endpoint telemetry (preconfigured  for [02-Endpoint-Compromise-Detection-and-Threat-Containment](https://github.com/miholmestech/SOC-Analyst-Projects-/tree/main/02-Endpoint-Compromise-Detection-and-Threat-Containment))

---

## 3. Steps Taken: Wazuh Multi-Host SIEM Lab Deployment

---

### 3.1 Download and Install Ubuntu Server

Step one was downloading the **Ubuntu Server 24.04.3 LTS ISO**.  
I attached the Ubuntu `.iso` file to a virtual machine, started the VM, and completed the Ubuntu Server installation.

Since the purpose of this system was to function as a **multi-host SIEM server**, I immediately configured networking:

- Changed VM network mode to **Bridged Adapter**

This allows the Ubuntu server to communicate directly with other machines on the same network instead of being hidden behind NAT.

### 3.2 Update and Prepare the System

After installation, I opened the terminal and ensured Ubuntu had all updates and required packages installed.

`sudo apt update && sudo apt upgrade -y`

`sudo apt install curl unzip wget apt-transport-https -y`

### 3.3 Install Wazuh via CLI

Wazuh provides a single-node install script that installs:

`Wazuh Manager`

`Indexer`

`Dashboard (web interface)`

 Downloaded and executed the script:

```curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh```

```sudo bash wazuh-install.sh -a```
This process took several hours and ran overnight. To verify the system was still active during installation, I monitored running processes: `top` Once installation completed, the script generated:

Dashboard URL: `https://<server-ip>`

Username: `admin`

Password: `Randomly generated password`

### 3.4 Accessing the Wazuh Dashboard 
From my host machine browser, I navigated to: `https://<server-ip>`
After logging in with the generated credentials, I confirmed the dashboard was operational and Ubuntu logs were successfully visible.
At this point, the SIEM server was fully deployed.

### 3.5 Adding a Windows Endpoint

To build a **multi-host SIEM environment**, I added a **Windows 10 VM** as an endpoint.

The Windows VM was already configured with:

- Sysmon telemetry  
- Basic logging  

Steps taken:

- Changed the Windows VM network mode to **Bridged Adapter**  
- Ensured both VMs were on the same network  

**Why bridged networking?**  
When a VM uses NAT, it hides behind the host machine.  
Bridged mode allows each VM to appear as its own device on the network so the SIEM server and endpoint can communicate directly.

### 3.6 First Troubleshooting Phase

The Windows agent appeared in Wazuh but was unable to fully connect.

- Wazuh could detect the Windows agent  
- The agent could not successfully establish communication with the manager  

This marked the first troubleshooting phase within the active lab environment.

### 3.7 Troubleshooting: Windows Agent Connection Failure

Although the Windows agent appeared in the Wazuh dashboard, it failed to fully connect to the manager.  
This indicated that the agent could see the server but was not properly configured to communicate with it.

After investigation, I identified that the Wazuh agent configuration on the Windows endpoint still contained the default manager address. 

![image](https://github.com/miholmestech/soc-detection-lab/blob/main/01%20Multi%20Host%20SIEM%20with%20Sysmon/Screenshots/Screenshot%202026-02-10%20213117.png)


To resolve this:

1. Located the Wazuh agent configuration file on the Windows VM: `C:\Program Files (x86)\ossec-agent\ossec.conf`
2. Opened the file using **Notepad as Administrator**
3. Updated the manager IP address to match the Ubuntu Wazuh server:

`xml
<server>
<address>SERVER-IP-HERE</address>
</server>`

4. Saved the file and restarted the Wazuh agent service via PowerShell: `Restart-Service WazuhSvc` 
5. After restarting the service, the agent successfully established communication with the Wazuh manager and began sending telemetry to the dashboard.

This confirmed successful multi-host SIEM communication between the Ubuntu server and Windows endpoint.
![image](https://github.com/miholmestech/soc-detection-lab/blob/main/01%20Multi%20Host%20SIEM%20with%20Sysmon/Screenshots/Screenshot%202026-02-10%20215230.png)

### 3.8 Sysmon Integration for Threat Detection

After the Windows agent successfully connected to the Wazuh manager, the next step was confirming that **Sysmon telemetry** was being ingested into the SIEM.

#### Initial Validation

First, I checked whether Sysmon logs were visible inside Wazuh.

- No Sysmon logs appeared in the Wazuh dashboard  
- This indicated either:
  - Sysmon was not installed  
  - Sysmon logs were not being forwarded  
  - Wazuh was not configured to read the correct log source
 
  ![image](https://github.com/miholmestech/soc-detection-lab/blob/main/01%20Multi%20Host%20SIEM%20with%20Sysmon/Screenshots/Screenshot%202026-02-10%20220444.png)

#### Confirm Sysmon Installation

I verified whether Sysmon was installed on the Windows endpoint.

- Confirmed Sysmon service was present  
- Confirmed logs existed locally
- Using Event Viewer: Applications and Services Logs → Microsoft → Windows → Sysmon → Operational

Both Sysmon and the Wazuh agent were confirmed to be running on the Windows 10 endpoint.  
However, Wazuh was not ingesting Sysmon logs into the SIEM.

This indicated that Sysmon was functioning locally but the Wazuh agent was not configured to read the correct event channel.

### Resolution

1. Located the Wazuh agent configuration file on the Windows VM: `C:\Program Files (x86)\ossec-agent\ossec.conf`
2. Opened the file using **Notepad as Administrator**
3. Added/verified the Sysmon event channel entry:

`xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>`


Saved the configuration file.
Restarted the Wazuh agent service: Restart-Service WazuhSvc
Validation After restarting the agent, Sysmon logs began appearing in Wazuh. Endpoint telemetry confirmed in dashboard. Multi-source log ingestion validated.
This confirmed successful integration of Sysmon telemetry into the SIEM environment.

![image](https://github.com/miholmestech/soc-detection-lab/blob/main/01%20Multi%20Host%20SIEM%20with%20Sysmon/Screenshots/Screenshot%202026-02-10%20232710.png)

---

## 4. Validation

Wazuh dashboard accessible

Windows agent successfully registered

Sysmon logs visible in SIEM

Endpoint telemetry actively ingesting

## 5. Security Considerations
Screenshots were sanitized to remove sensitive internal IP addresses. Host identifiers were retained where necessary to preserve technical clarity for documentation purposes.

## 6. Outcome
A functional multi-host SIEM environment was successfully deployed.
This environment establishes the foundation for detection engineering and investigation work.
Next Steps
- Investigate ingested telemetry
- Write Windows and Linux investigation reports
- Build detection logic
- Develop alert scenarios
