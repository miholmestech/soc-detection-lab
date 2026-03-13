# SOC Detection Lab

This repository documents a hands-on SOC detection lab built to simulate how security analysts monitor, detect, and investigate real activity across a small multi-host environment.

Windows and Linux systems send telemetry into a centralized SIEM while a separate attacker machine generates realistic activity for detection testing.

Why a multi-host environment? Because I had the knowledge to build one so it was time to turn theory into hands-on experience.

*Look around. Be kind. You're inside my brain.*

---

## Objective

Build and document a realistic SOC lab where I can:
- Collect telemetry from multiple endpoints into a central SIEM  
- Expand attack surface to improve detection fidelity  
- Create concrete detection use cases (RDP, SSH, PowerShell, etc.)  
- Practice structuring investigations like a junior SOC analyst  
- Iterate on lab design as new requirements emerge  

---

## Lab Architecture Overview

This lab simulates a small monitored enterprise network with separate infrastructure for telemetry collection and adversary simulation.

### Monitored Environment
- Windows 10 endpoint with Sysmon + Wazuh agent  
- Dedicated Linux endpoint for clean telemetry and investigation prep  
- Ubuntu Wazuh server (manager, indexer, dashboard)

### Adversary System
- Kali Linux VM used as an external attacker  
- Not enrolled in Wazuh monitoring  
- Used to generate realistic activity including:
  - RDP attempts  
  - SSH brute-force attempts  
  - Network scans  
  - Suspicious traffic  

This separation mirrors real-world SOC conditions where malicious activity originates outside the monitored environment and must be detected through endpoint and network telemetry rather than direct visibility into the attacker system.

---

## Repository Structure

| Folder | Description |
|--------|-------------|
| [01_Multi_Host_SIEM_with_Sysmon](./01_Multi_Host_SIEM_with_Sysmon/) | Initial lab deployment and telemetry validation |
| [02_Kali_Attack_Box_Integration](./02_Kali_Attack_Box_Integration/) | Attacker VM setup and network segmentation |
| [03_Linux_Endpoint_Telemetry_Integration](./03_Linux_Endpoint_Telemetry_Integration/) | Dedicated endpoint added to improve log clarity and investigation accuracy |
| [04_RDP_Investigation](./04_RDP_Investigation/) | Detection and investigation workflow (in progress) |
| [05_SSH_Investigation](./05_SSH_Investigation/) | Upcoming Linux-focused detection scenarios |

---

## What You’ll Find Here

- Environment deployment and configuration  
- Detection engineering experiments  
- Investigation workflows and timelines  
- Lessons learned from lab iteration  
- Architecture decisions made along the way  

This lab is always a work in progress. As detection needs evolve, the environment evolves with it.

---

## 👩🏿‍💻 Author

**Michelle Holmes**  
*SOC Analyst | Blue Team Focus*  

[GitHub](https://github.com/miholmestech) | [LinkedIn](https://www.linkedin.com/in/michelle-holmes-252441291/)

