# SIEM Deployment & Log Ingestion

## 1. Objective

Provision an Ubuntu server and deploy Wazuh to establish a centralized SIEM, then onboard an existing Windows 10 lab endpoint with Sysmon configured to forward telemetry for multi-host detection testing. This documents the deployment of the SIEM infrastructure only. Detection scenarios and investigations built on this environment are documented separately.

## 2. Environment
- Ubuntu Server (Wazuh Manager + Dashboard)
- Wazuh install via CLI 
- - Windows 10 endpoint (preconfigured in prior lab project [01-Windows-VM-Setup-and-Baseline](https://github.com/miholmestech/SOC-Analyst-Projects-/tree/main/01-Windows-VM-Setup-and-Baseline))
- Sysmon configured for endpoint telemetry (preconfigured  for [02-Endpoint-Compromise-Detection-and-Threat-Containment](https://github.com/miholmestech/SOC-Analyst-Projects-/tree/main/02-Endpoint-Compromise-Detection-and-Threat-Containment))

## 3. Steps Taken



## 4. Issues Encountered

Installation delays

Service startup problems

Agent registration issues

Dashboard access troubleshooting

## 5. Validation

Wazuh dashboard accessible

Windows agent successfully registered

Sysmon logs visible in SIEM

Endpoint telemetry actively ingesting

## 6. Security Considerations

Screenshots were sanitized to remove internal IPs. host identifiers were considered, but were needed to be visable for clear documentation. 
## 7. Outcome
A functional multi-host SIEM environment was successfully deployed.
This environment establishes the foundation for detection engineering and investigation work.
Next Steps
- Investigate ingested telemetry
- Write Windows and Linux investigation reports
- Build detection logic
- Develop alert scenarios
