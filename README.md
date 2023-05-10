# SOC Implementation in Azure (Live Traffic)
<a href="https://ibb.co/R0zCB5Y"><img src="https://i.ibb.co/G2cVthn/Cloud-SOC.png" alt="Cloud-SOC" border="0" width="1000"></a>

## Introduction

In this project, a honeynet is created to simulate attacks on endpoints and measure key security metrics. 

2 sentinel watchlists are created with geo ipv4 data and using the logs forwarded to log analytics workspace from various endpoints, we build attack maps, trigger alerts, and create incidents. 

I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show ar

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with **public endpoints** visible to the Internet

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as **private endpoint (subnet)**

## Attack Maps Before Hardening / Security Controls 
<a href="https://ibb.co/5rpZZVy"><img src="https://i.ibb.co/X70HHTg/nsg-malicious-allowed-in-BEFORE.png" alt="nsg-malicious-allowed-in-BEFORE" border="0"></a> <br>
<a href="https://ibb.co/870Cw7s"><img src="https://i.ibb.co/gR328R9/syslog-ssh-auth-fail-before.png" alt="syslog-ssh-auth-fail-before" border="0"></a><br>
<a href="https://ibb.co/wwHKQLm"><img src="https://i.ibb.co/LJwCgP7/windows-rdp-smb-auth-fail-before.png" alt="windows-rdp-smb-auth-fail-before" border="0"></a><br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-05-03T05:45:59.0834638Z
Stop Time 2023-05-04T05:45:59.0834638Z

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 2973
| Syslog                   | 40
| SecurityAlert            | 1
| SecurityIncident         | 104
| AzureNetworkAnalytics_CL | 137

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.``` <br>

<a href="https://ibb.co/k1qwB1P"><img src="https://i.ibb.co/nCQYmCK/nsg-malicious-allowed-in-AFTER.png" alt="nsg-malicious-allowed-in-AFTER" border="0"></a>

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-05-05T00:43:20.3152315Z
Stop Time	2023-05-06T00:43:20.3152315Z

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 1173
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

We were able to achieve an secure score on **84%** for Microsoft Defender.

Key Implementations
- Private Endpoints for Resource Level. i.e blobs in storage account, key vault
- NSG hardening. Endpoint protection by moving public endpoints to private.
- Enabling MFA
- Configuring Azure Firewall
- Enable DDOS protection on Azure Virtual Network

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
