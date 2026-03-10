# SOC Investigation: Unsuccessful RDP Brute‑Force Attempts Detected in Windows Security Logs

## 1. Executive Summary

Windows Security Logs on the target host show multiple failed RDP‑style network logon attempts (Event ID 4625, Logon Type 3) from a single external workstation “KALI” at IP 192.168.56.104, followed by service and privileged logon activity on the host. The pattern is consistent with password‑guessing or brute‑force attempts over RDP where Network Level Authentication (NLA) prevents full interactive sessions from being established. No unauthorized successful interactive logons were observed during this period; however, an account with elevated privileges was used on the system and should be validated as legitimate administrative activity.

---

## 2. Alerts Overview

| Event ID | Description                      | Why It Matters                                                                 |
|---------|----------------------------------|--------------------------------------------------------------------------------|
| 4624    | Successful logon                 | Confirms account access; used to track authorized logins.                      |
| 4625    | Failed logon                     | Shows failed login attempts; repeated failures can indicate brute‑force attacks. |
| 4634    | Logoff                           | Indicates when sessions end; helps correlate logon/logoff patterns.           |
| 4648    | Logon using explicit credentials | Detects potential credential misuse or lateral movement.                       |
| 4672    | Special privileges assigned      | Identifies accounts gaining elevated rights; potential privilege misuse.       |

---

## 3. Timeline of Events

| Timestamp   | Event ID | Notes |
|------------|----------|-------|
| 4:29:09 PM | 4625     | Multiple failures with Logon Type 3 from “KALI” indicate repeated network authentication attempts, consistent with RDP activity where NLA causes RDP failures to appear as Type 3 instead of Type 10. |
| 4:29:59 PM | 4624     | Successful service logon (Logon Type 5); expected system behavior but should be correlated with the specific account and service involved. |
| 4:29:59 PM | 4672     | “Special privileges assigned” shows the logon session had sensitive rights (for example SeDebugPrivilege or other admin‑level rights) and should normally only be associated with SYSTEM or known admins. |
| 4:34:29 PM | 4648     | Logon with explicit credentials suggests alternate credentials were used for a task (process, network access, or RDP), which can indicate lateral movement or credential misuse if not tied to an approved admin action. |
| 4:34:30 PM | 4634     | Session logoff (Logon Type 2) confirms session termination. |
| 4:53:14 PM | 4625     | Failed logon attempt (Logon Type 3) – workstation name KALI, IP 192.168.56.104. |
| 5:04:17 PM | 4625     | Failed logon attempt (Logon Type 3) – workstation name KALI, IP 192.168.56.104. |

---

## 4. Analysis / Evidence

Between 4:29 and 5:04, the host recorded repeated failed network logon attempts (Event ID 4625, Logon Type 3) from workstation “KALI” (192.168.56.104), consistent with RDP authentication attempts against the system while NLA was enabled. During this window, the system also recorded a service logon (4624, Type 5) and a privileged logon event (4672), followed by an explicit credential use event (4648) and session logoff (4634); these privileged actions appear local/administrative and require validation as sanctioned activity.

- Screenshot 01: Failed logon from KALI, IP 192.168.56.104, Logon Type 3 – demonstrates initial brute‑force attempt.  
- Screenshot 02: Successful service logon (Type 5) – expected system activity, included for correlation.  
- Screenshot 03: Privilege assignment (4672) – highlights an account with elevated rights.  
- Screenshot 04: Explicit credential login (4648) – shows use of alternate credentials.  
- Screenshot 05: Session logoff (4634) – session closure after privileged activity.  
- Screenshot 06 & 07: Additional failed logon attempts from KALI – reinforce repeated unauthorized login activity from the same source IP.  

---

## 5. Findings

- All failed logon attempts in this window originated from a single IP source, 192.168.56.104 (workstation “KALI”), indicating a focused attack source rather than broad spraying.  
- None of the observed failed attempts were legitimate or successful; there is no evidence of a successful interactive or remote interactive logon from this source during the timeframe reviewed.  
- RDP attempts were logged primarily as Logon Type 3 (Network) rather than Type 10 (Remote Interactive), which is consistent with environments using NLA and newer Windows versions where RDP activity may be recorded as Type 3. This indicates that full RDP sessions were never established during the failed attempts.  

---

## 6. Recommendations

- Reset passwords for accounts with repeated failed logons and review them for weak or reused passwords.  
- Enable multi‑factor authentication (MFA) for all remote access paths, including RDP and VPN.  
- Continue monitoring for unusual login patterns, especially bursts of 4625 failures from a single source or targeting multiple accounts.  
- Confirm that RDP is required on this host; if not, disable it at the host and firewall level, or restrict access to a dedicated management subnet or jump server only.  
- Create a Wazuh detection rule to correlate 4625 (failed logon), 4624 (successful logon), and 4672 (special privileges) so you receive high‑fidelity alerts on suspicious privileged logons following a burst of failures; consider adding Active Response to automatically block offending IPs after a defined threshold.  
- Block the suspicious IP 192.168.56.104 at the host firewall and any upstream security controls, and continue to monitor for attempts from new or shifted IPs.  

