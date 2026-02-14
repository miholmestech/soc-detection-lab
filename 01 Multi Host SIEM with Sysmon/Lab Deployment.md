# SIEM Deployment & Log Ingestion

## 1. Objective

Provision an Ubuntu server and deploy Wazuh to establish a centralized SIEM, then onboard an existing Windows 10 lab endpoint with Sysmon configured to forward telemetry for multi-host detection testing. This documents the deployment of the SIEM infrastructure only. Detection scenarios and investigations built on this environment are documented separately.

## 2. Environment
- Ubuntu Server (Wazuh Manager + Dashboard)
- Wazuh install via CLI 
- Windows endpoint
- Sysmon configured for endpoint telemetry (configured perviously for [02-Endpoint-Compromise-Detection-and-Threat-Containment](02-Endpoint-Compromise-Detection-and-Threat-Containment))

## 3. Steps Taken



## 4. What went wrong? 


## 5. Validation


## 6. Security Considerations

Screenshots were sanitized to remove internal IPs. host identifiers were considered by were needed for clear documentation. 
## 7. Outcome
Establish a working SIEM environment before moving into investigation and detection work.
Next steps will include:
- Investigating telemetry
- Writing Windows and Linux investigation reports
- Building detection logic
