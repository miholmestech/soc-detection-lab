## Adding a Dedicated Linux Endpoint

While preparing for SSH and ongoing RDP attack simulations, I realized attacking the Wazuh server added too much background noise from system processes.  

To get cleaner logs and make investigations easier, I added a dedicated Linux endpoint and integrated it into Wazuh.  

This gives better-quality logs for future detection exercises and lets me run more controlled attack simulations across multiple endpoints.

