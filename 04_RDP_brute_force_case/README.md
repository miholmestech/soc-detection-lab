# RDP Brute Force Case Study

This scenario demonstrates the SOC workflow for analyzing Remote Desktop Protocol (RDP) authentication telemetry and developing detection logic for suspicious login activity.

Attack traffic originated from a segmented Kali Linux attacker VM targeting a Windows 10 endpoint monitored by Wazuh.

The lab explores how **Network Level Authentication (NLA)** affects Windows authentication telemetry and how those changes impact SIEM detection strategies.

## Steps Covered

1. Simulated RDP authentication attempts from a Kali attacker VM
2. Windows security telemetry generation and log analysis
3. Investigation of authentication events within Wazuh SIEM
4. Development of detection logic for identifying RDP brute-force behavior

## Artifacts

| Artifact | Description |
|--------|--------|
| Investigation | SOC-style analysis of RDP authentication telemetry |
| Detection Use Case | SIEM rule logic for detecting repeated RDP login attempts |

