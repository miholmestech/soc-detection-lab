# SSH Brute Force Detection Use Case

## Detection Objective

Detects repeated SSH authentication failures that may indicate a brute force attack attempting to guess valid credentials on a Linux system.

This detection is designed to identify suspicious login activity originating from a single source IP ( In This case IP 192.168.56.1) generating multiple failed authentication attempts within a short time window.

---

## Detection Strategy

The detection focuses on authentication failures recorded in the Linux authentication logs.

Repeated failed login attempts originating from the same IP address are correlated to identify potential credential brute-force behavior.

Indicators include:

- Multiple **Failed password** events
- High frequency authentication attempts
- Repeated attempts against the same account
- Consistent source IP address

---

## Log Sources

| Log Source | Description |
|------------|-------------|
| `/var/log/auth.log` | Linux authentication events including SSH login attempts |
| Ubuntu Agent | Collects and forwards authentication telemetry to the SIEM |
| Wazuh SIEM | Correlates events and triggers detection alerts |

---

## Detection Logic

The detection rule monitors SSH authentication failures and triggers an alert when repeated failures occur from the same source IP.

### Rule Conditions

- Event contains **"Failed password"**
- SSH authentication attempt detected
- Multiple failures from the same source IP
- Activity occurs within a defined time window

```
Example logic:
IF:
 5 or more failed SSH login attempts
 FROM the same source IP
 WITHIN 2 minutes
THEN:
 Trigger alert → Potential SSH brute force attack
```

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|------|------|------|
| Credential Access | Brute Force | T1110 |
| Credential Access | Password Guessing | T1110.001 |
| Lateral Movement | Remote Services: SSH | T1021.004 |

---

## Detection Validation

The detection rule was validated by simulating repeated SSH login attempts from a Kali Linux attacker VM targeting the Ubuntu endpoint.

The simulated attack generated multiple failed authentication events in `/var/log/auth.log`, which were successfully ingested by the Wazuh SIEM and triggered authentication failure alerts.

This confirms the detection logic can identify repeated login attempts consistent with brute-force credential guessing behavior.

---

## Tuning Considerations

To reduce false positives in production environments, detection thresholds may require adjustment.

Potential tuning strategies include:

- Adjusting the failed login threshold
- Increasing or decreasing the time window
- Excluding trusted administrative IP ranges
- Monitoring login attempts against privileged accounts separately

---

## Detection Outcome

This detection rule provides early visibility into SSH brute force activity targeting Linux systems.

When combined with endpoint telemetry and SOC investigation workflows, this detection enables analysts to identify credential guessing attacks and respond before unauthorized access is achieved.


