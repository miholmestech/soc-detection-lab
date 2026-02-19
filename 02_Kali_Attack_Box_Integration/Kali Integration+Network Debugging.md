# Kali Attack VM Integration & Network Segmentation

> I built it.  
> I broke it.  
> I fixed it.  
> I understand it.

---

## Objective

The goal of this project was to integrate a **stand-alone Kali Linux VM** into my existing SOC lab without directly integrating it into my SIEM stack. Kali serves as my **attacker machine**, used to simulate realistic external behavior against my Windows endpoint. To keep the lab realistic and properly segmented:

- Kali must appear **outside** the monitored network  
- Kali must still be able to **reach the Windows endpoint**  
- Kali must **not sit on the same adapter as the SIEM**

To accomplish this, I designed a segmented network using a **dual-adapter configuration**.

### Kali VM
- Adapter 1: NAT → internet access (updates/tools)
- Adapter 2: Host-Only → attacker-to-victim traffic

### Windows 10 VM
- Adapter 1: Connected to SIEM network (for Wazuh telemetry)
- Adapter 2: Host-Only network (for attacker communication)

This allows Kali to simulate attacker behavior while keeping the SIEM environment isolated.

---

## Initial Lab State

- Windows 10 endpoint  
  - Sysmon installed  
  - Logs forwarding to Wazuh  
- Ubuntu Wazuh SIEM server  
- Kali Linux attacker VM (stand-alone)

---

## Problems Encountered

Several issues appeared immediately once segmentation was introduced.

### 1. RDP Disabled on Windows
Windows 10 did not allow Remote Desktop connections by default.  
Since Kali will simulate attacker behavior via RDP, this had to be enabled.

### 2. No ICMP Connectivity
Even after placing both machines on the host-only network:

- Kali could not ping Windows  
- Windows blocked ICMP  
- No communication between hosts  

### 3. Kali Lost Internet Access
Once communication between Kali and Windows was working:

- Kali no longer had internet connectivity  
- Tools could not be installed  
- RDP testing became impossible  

This indicated an adapter configuration conflict.

---

## Investigation Process

### Step 1  Fix Windows Connectivity

Enabled Remote Desktop:

`System → Remote Desktop → Allow connections`

ICMP still failed.

Firewall toggling did not resolve the issue, so ICMP was manually allowed via PowerShell (Admin):

[```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4" -Protocol ICMPv4```] 

Retested `ping` → Success
Kali and Windows could now communicate.

### Step 2  Kali Networking Failure

Kali previously had internet access but lost it after adapter changes.

Troubleshooting steps:

1. Boot Kali 
2. Check adapter configuration in VirtualBox
3. Switch between NAT / Bridged / Host-Only
4. Test connectivity  `ping google.com`
5. Inside Kali terminal I typed  `ip a` 

Result:

Kali only received a DHCP address from the host-only adapter, not the NAT adapter. This meant: Kali could reach Windows, but had no route to the internet

## Root Cause
Kali pulled a DHCP lease only from the host-only network.The NAT adapter was not properly assigning an IP address.
Because of this:
 -Host-only traffic worked
 -Internet routing failed

 ## Resolution

Steps taken:
1. Restarted VirtualBox networking services
2. Verified adapter order
3. Ensured NAT adapter was enabled
4. Rebooted Kali
5. Confirmed DHCP lease assignment

After restarting network manager:

-Kali received a valid NAT IP and internet connectivity restored
-Host-only communication remained intact

## Lessons Learned

-Network segmentation matters. 

-Adapter planning determines whether a lab behaves realistically.

-Windows blocks ICMP by default

-Firewall rules must be manually enabled for testing.

-Dual adapters can conflict so prepared. 

-Host-only networks can override DHCP behavior if not configured carefully.

-Troubleshooting is just apart of the process.

## Lab Impact

This attacker VM now allows me to:

-Simulate attacker activity from Kali

-Generate telemetry into Wazuh

-Trigger RDP-based alerts

-Build detection engineering scenarios

-Run future investigations

*This system will be used in upcoming SOC lab projects and detection testing.*




