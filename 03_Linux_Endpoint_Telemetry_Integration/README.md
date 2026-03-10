## Adding a Dedicated Linux Endpoint

**Module Goal**: Deployed a dedicated Linux endpoint for high‑quality SSH telemetry, reducing noise from attacking the Wazuh server itself. Enables brute‑force detection, login analysis, and persistence hunting with clean, multi‑host context.

**New Agent Details**:
- OS: Ubuntu 24.04.4 LTS VM.  

![Ubuntu agent added](https://github.com/miholmestech/soc-detection-lab/blob/main/03_Linux_Endpoint_Telemetry_Integration/Screenshots/ubuntu-agent.png)

Now I have two active agents: Linux Ubuntu and Windows 10, establishing multi-host telemetry.
![2 Active Agents](https://github.com/miholmestech/soc-detection-lab/blob/main/03_Linux_Endpoint_Telemetry_Integration/Screenshots/2%20agents%20setup.png)
