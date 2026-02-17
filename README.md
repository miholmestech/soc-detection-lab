# SOC Detection Lab

This lab showcases how I use a SIEM to detect and investigate real security events across a small multi-host environment (Windows + Linux).

Why a multi-host environment? Because I had the knowledge to build one and it was time to turn theory into hands-on experience.  
Look around. Be kind. You’re inside my brain.

## Objective

Build and document a realistic SOC lab where I can:

- Collect telemetry from multiple endpoints into a central SIEM  
- Create concrete detection use cases (RDP, SSH, PowerShell, etc.)  
- Practice structuring investigations like a junior SOC analyst  

---

## Lab Architecture Overview

This lab simulates a small monitored enterprise environment with a separate attacker system to generate realistic detection scenarios and investigation workflows.

### Monitored Stack
- Windows 10 endpoint with **Sysmon** and **Wazuh agent**
- Ubuntu Wazuh server (**SIEM and log analysis**)
- Network detection layer *(planned)*: **Suricata IDS**

### Adversary System
- Kali Linux VM used as an **external attacker host**
- Intentionally **not enrolled** in Wazuh or endpoint monitoring
- Used to simulate:
  - network scans
  - brute force attempts
  - suspicious traffic
  - other adversary behaviors

This separation mirrors real-world SOC conditions, where malicious activity originates outside the monitored environment and must be detected through network and endpoint telemetry rather than direct visibility into the attacker system.

---

## What You’ll Find Here

- **Environment setup:** How I deployed Wazuh, added agents, and verified telemetry  
- **Detection use cases:** Step-by-step attack simulations and alert generation  
- **Investigation workflows:** Timelines, pivot fields, and findings  
- **Lessons learned:** What I’d improve in a real SOC environment

---

## 👩🏿‍💻 Author

**Michelle Holmes**  
*SOC Analyst | Blue Team Focus*  
[GitHub](https://github.com/miholmestech) | [LinkedIn](https://www.linkedin.com/in/michelle-holmes-252441291/)