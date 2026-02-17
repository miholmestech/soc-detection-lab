# SOC Detection Lab

This lab showcases how I use a SIEM to detect and investigate real security events across a small multi-host environment (Windows + Linux). 

## Objective

Build and document a realistic SOC lab where I can:
- Collect telemetry from multiple endpoints into a central SIEM
- Create concrete detection use cases (RDP, SSH, PowerShell, etc.)
- Learning how to struction investigations like a junior SOC analyst

  ## Lab Overview

- **SIEM:** Wazuh running on Ubuntu Server
- **Endpoints:** Windows 10 with Sysmon, Ubuntu Linux server
- **Telemetry:** Sysmon process creation, auth logs, basic network activity
- **Network:** Home lab network with segmented VMs
