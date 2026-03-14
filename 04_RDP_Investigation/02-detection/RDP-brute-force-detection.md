Attack originated from segmented Kali attacker VM [see integration here](https://github.com/miholmestech/soc-detection-lab/blob/main/02_Kali_Attack_Box_Integration/Kali%20Integration%2BNetwork%20Debugging.md)

---

# RDP Authentication Detection & NLA Telemetry Analysis (Detection Engineering Lab)

This lab focuses on how RDP authentication attempts are logged in a Windows 10 + NLA environment and how to design SIEM rules that correctly detect brute‑force activity.

---

## 1. Objective

Develop and test detection logic for unauthorized RDP authentication attempts, and evaluate how environmental controls (such as Network Level Authentication – NLA) impact Windows logon telemetry and SIEM visibility.

---

## 2. Lab Environment

### Components

- **Windows 10 Endpoint** (Sysmon installed)
- **Wazuh SIEM**
- **Kali Linux Attacker VM**
- Remote Desktop enabled
- Network Level Authentication (NLA) enabled initially

Testing was conducted in a controlled virtual lab environment. RDP authentication attempts were initiated from a dedicated Kali Linux attacker virtual machine to the Windows 10 endpoint.

Initial testing was performed with **Network Level Authentication (NLA) enabled**.  

NLA requires authentication before a full RDP session is established, which can impact how authentication events are logged.

---

## 3. Telemetry Generation Method

Simulated RDP login attempts (both failed and successful) were generated from the attacker VM using standard RDP client tools.

### Test Scenarios

- Valid username + incorrect password
- Invalid username + invalid password
- Successful authentication
- Session logoff

Telemetry was reviewed in:

- Windows Security Event Logs
- Wazuh SIEM dashboard

---

## 4. Observed Telemetry

The following Windows Security Events were observed:

| Event ID | Description |
|----------|------------|
| 4625 | Failed logon |
| 4624 | Successful logon |
| 4634 | Logoff |
| 4672 | Special privileges assigned |
| 4648 | Explicit credential use |

### Logon Types Observed

- **Logon Type 3** (Network)
- Background service types (e.g., 5)

### Logon Types Not Initially Observed

- **Logon Type 10 (RemoteInteractive)**

This was unexpected, as traditional RDP sessions commonly generate logon type 10.

---

## 5. Environmental Findings

### Initial Condition: NLA Enabled

With NLA enabled:

- Fake username + fake password → Generated **Event ID 4625**
- Valid username + wrong password → Did not consistently generate expected telemetry
- Successful login → Generated **Event ID 4624**
- Logoff → Generated **Event ID 4634**
- Privilege assignment → Generated **Event ID 4672**
- Explicit credential usage → Generated **Event ID 4648**

### Example Failed Logon Event

- **Event ID:** 4625
- **Logon Type:** 3
- **Account Name:** `fakeusername`
- **Failure Reason:** Unknown user name or bad password
- **Source IP:** 192.168.56.104 (Kali attacker VM)

### Key Finding

Although RDP was used, failed attempts were logged primarily as **Logon Type 3 (Network)** rather than **Logon Type 10 (RemoteInteractive)**.

This behavior is consistent with environments where **NLA performs authentication before session establishment**, meaning a full RDP session is never created during failed attempts.

---

## 6. Detection Engineering Considerations

Detection logic relying solely on:

- Event ID 4625  
- Logon Type 10  

would miss RDP authentication attempts in environments where NLA is enabled.

### Recommended Detection Logic Should Include:

- Event ID 4625
- Logon Type 3 and/or 10
- Source IP frequency analysis (brute force behavior)
- Status and Substatus codes
- Account enumeration patterns

This reinforces the importance of environmental awareness when building SIEM detection rules.

---

## 7. Comparative Testing: NLA Disabled

To further analyze telemetry behavior, NLA was temporarily disabled in the lab environment.

### Expected Outcome

- Failed RDP attempts generating Logon Type 10

### Actual Outcome

- Logon Type 10 was not consistently observed.

### Interpretation

This suggests additional environmental or client-side factors may influence logon type generation, including:

- RDP client implementation (e.g., xfreerdp3 behavior)
- Credential handling method
- Session establishment state
- Windows security policy configuration

---

## 8. Lessons Learned

- Authentication telemetry is heavily influenced by environmental security controls.
- NLA changes authentication flow and impacts logon type visibility.
- Detection engineering must be based on observed telemetry, not assumptions.
- Lab inconsistencies provide investigative insight rather than representing failure.

---

## Conclusion

This lab demonstrated that RDP detection logic must account for environmental factors such as Network Level Authentication (NLA).  

Effective detection engineering requires validating assumptions against real telemetry and adapting detection rules accordingly.
## Detection Rule

### Wazuh Detection Logic

This rule identifies repeated failed RDP authentication attempts from a single source IP.

**Logic:**
- Event ID 4625
- Logon Type 3 or 10
- Multiple attempts from same source IP within timeframe

### Example Rule Concept

If:
- 5+ failed logins
- Same source IP
- Within 2 minutes

Trigger alert: Potential RDP brute force
