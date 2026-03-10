## Adding a Dedicated Linux Endpoint

While preparing for SSH and ongoing RDP attack simulations, I realized attacking the Wazuh server added too much background noise from system processes.  

To get cleaner logs and make investigations easier, I added a dedicated Linux endpoint and integrated it into Wazuh.  

This gives better-quality logs for future detection exercises and lets me run more controlled attack simulations across multiple endpoints.

![Ubuntu agent added](https://github.com/miholmestech/soc-detection-lab/blob/main/03_Linux_Endpoint_Telemetry_Integration/Screenshots/ubuntu-agent.png)

Now I have two active agents—Linux Ubuntu and Windows 10, establishing multi-host telemetry.
![2 Active Agents](https://github.com/miholmestech/soc-detection-lab/blob/main/03_Linux_Endpoint_Telemetry_Integration/Screenshots/2%20agents%20setup.png)
