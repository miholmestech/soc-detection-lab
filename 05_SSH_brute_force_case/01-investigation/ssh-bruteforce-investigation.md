# SSH Brute Force Investigation

## Executive Summary

A brute force attack targeting the SSH service was detected on the Linux endpoint. Multiple failed authentication attempts were observed from a single source IP within a short time window.

Log analysis confirmed repeated login failures consistent with credential brute-force behavior. The activity was investigated using Wazuh SIEM alerts and Linux authentication logs to determine the scope, source, and potential impact of the attack.

---

## Alert Overview

SSH authentication failures were detected on the Ubuntu endpoint and recorded in `/var/log/auth.log`.

The events were ingested by the Wazuh SIEM through the deployed agent and triggered **Rule 5760 – SSH authentication failed**.

Repeated failures escalated to a brute-force alert (**Rule 2502**), indicating potential credential guessing activity against the SSH service.

The alerts were mapped to the following MITRE ATT&CK techniques:

- **T1110.001 – Password Guessing**
- **T1021.004 – Remote Services: SSH**

Investigation confirmed that the simulated attacker system successfully generated detectable brute-force behavior which was captured at the endpoint and correlated within the SIEM platform.

---

## Timeline

| Time | Event |
|-----|------|
| 10:19:54 | Failed SSH login attempt |
| 10:20:00 | Failed SSH login attempt |
| 10:20:05 | Failed SSH login attempt |
| 10:21:32 | Failed SSH login attempt |
| 10:21:55 | Connection closed |

---

## Investigation Steps

1. SSH login attempts were initiated from the Kali attacker VM targeting the Ubuntu endpoint.
2. Multiple failed authentication attempts were recorded in `/var/log/auth.log`.
3. Wazuh ingested the logs through the deployed agent and triggered **Rule 5760 (sshd authentication failed)**.
4. Continued failed authentication attempts escalated to a brute-force alert (**Rule 2502**).

---

## Attack Analysis

Indicators observed during the investigation include:

- Repeated **"Failed password"** authentication attempts in `/var/log/auth.log`
- High frequency of login attempts originating from **192.168.56.1**
- Attempts targeting valid system usernames
- Multiple failures occurring within a short time window

These indicators are consistent with **SSH brute-force credential guessing behavior**.

Attempts targeted the account **michelle**, indicating that the attacker either knew a valid username or was attempting common account names.

---

## Evidence

Authentication failures were confirmed by reviewing the Linux authentication log.

Command used:
```
grep "Failed password" /var/log/auth.log
```
he output revealed repeated authentication failures originating from the attacker IP 192.168.56.1.

Each login attempt created a new SSH connection attempt, which is typical in brute-force activity. Connections were observed from multiple source ports, including 64091 and 53556.

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|------|------|------|
| Credential Access | Brute Force | T1110 |
| Credential Access | Password Guessing | T1110.001 |
| Lateral Movement | Remote Services: SSH | T1021.004 |

This activity maps primarily to **Credential Access**, where adversaries attempt to obtain valid credentials by repeatedly guessing passwords.

It also relates to **Lateral Movement**, since SSH is a remote service commonly used by attackers to move between systems once valid credentials are obtained.

---

## Recommendations

- Implement SSH rate limiting or account lockout policies  
- Restrict SSH access to trusted IP ranges  
- Enforce stronger authentication policies such as **SSH key-based authentication**  

---

## Conclusion

The investigation confirmed that repeated failed SSH authentication attempts originating from the Kali attacker VM triggered Wazuh detection rules and generated alerts consistent with brute-force credential guessing activity.

The SIEM successfully captured and correlated the events, demonstrating effective visibility into SSH authentication activity within the lab environment.

