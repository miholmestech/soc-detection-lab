
While preparing for SSH and continued RDP attack simulations, it became clear that attacking the Wazuh server would introduced excessive background noise from system processes.

To ensure cleaner telemetry and clearer understanding during investigations, I decided to add a dedicated Linux endpoint and integrated into Wazuh.

This adjustment improves the quality of logs used for future detection investigations and allows for more controlled attack simulation across multiple endpoints.
