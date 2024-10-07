# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Course of Action
- Establishing the honeynet: To start, I created the vulnerable environment with the Virtual Machines. This was done by disabling the firewall inside of the VM as well as allowing all ports and traffic to be received by the Network Security Group (NSG).

- Tracking and examination: The Azure infrastructure was meticulously configured to seamlessly ingest log sources from a multitude of resources into a dedicated log analytics workspace. Leveraging the advanced capabilities of Microsoft Sentinel, sophisticated attack maps were meticulously constructed, meticulously triggering highly precise alerts and meticulously generating comprehensive incidents, all meticulously derived from the meticulously collected and meticulously analyzed data.

- Tracking and evaluating security metrics: I monitored the unsecured environment for a full day, noting important security measurements during that time. This served as a starting point for comparison once I applied security improvements.

- Addressing and resolving security incidents: Following the resolution of incidents and identification of vulnerabilities, I proceeded to fortify the environment by implementing security best practices and incorporating Azure-specific recommendations.

- Analysis after implementing remediation measures: An additional 24-hour period was dedicated to the meticulous re-observation of the environment, facilitating a comprehensive evaluation of the security metrics. The resulting data was then meticulously juxtaposed with the initial baseline, enabling a rigorous comparative analysis.

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

In this project, I created a resource group in Azure and deployed both a Windows and a Linux virtual machine. To simulate vulnerability, I disabled Windows Firewall protection on the Windows machine via Remote Desktop Protocol (RDP) and modified its Network Security Group (NSG) to allow all inbound traffic using the wildcard *. For the Linux machine, I adjusted the NSG to permit all inbound traffic with the wildcard *.

In the "BEFORE" phase, all resources were publicly exposed to attract potential attackers. Both virtual machines had open NSGs and firewalls, allowing unrestricted access from any source. Storage accounts and databases were also publicly accessible, without using Private Endpoints for security. This insecure setup aimed to observe real-world cyberattack behavior.

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

To enhance the security of our architecture, I executed a comprehensive set of hardening measures and implemented robust security controls. These improvements included:

- **Network Security Groups (NSGs):** I tightened the NSGs to allow only inbound and outbound traffic from my designated public IP address, ensuring that only authorized traffic could access the virtual machines.
  
- **Built-in Firewalls:** I configured VM-specific firewalls to limit unauthorized access, tailoring rules to minimize potential attack vectors and protect resources.
  
- **Private Endpoints:** Public endpoints for Azure resources, such as storage accounts and databases, were replaced with Private Endpoints. This restricted access to within the virtual network, safeguarding sensitive resources from external threats and unauthorized access.

By comparing the security metrics before and after implementing these hardening measures, I demonstrated the significant improvement in the overall security posture of the Azure environment.

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/1qvswSX.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/G1YgZt6.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/ESr9Dlv.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-03-15 17:04:29
Stop Time 2023-03-16 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 19470
| Syslog                   | 3028
| SecurityAlert            | 10
| SecurityIncident         | 348
| AzureNetworkAnalytics_CL | 843

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8778
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

This project has been both challenging and rewarding as I set up a mini honeynet within Microsoft Azure to analyze real-world traffic. Using attack maps and KQL data, I was able to extract various metrics that helped paint a clear picture of an insecure versus a secure environment. It was incredibly satisfying to see everything come together, demonstrating how malicious traffic disappeared after implementing various security controls. Initially, when the resources were left vulnerable, I observed the IP addresses of attackers and the usernames they were attempting to use to access my virtual machines. After completing the hardening process and waiting for 24 hours, it was gratifying to find zero instances of malicious traffic attempting to reach my systems.

This project involved integrating diverse log sources into a dedicated Log Analytics workspace within Azure, while Microsoft Sentinel monitored and generated alerts based on the ingested logs. We measured key metrics in the vulnerable state, then reassessed after the security measures were in place. The results were clear—a significant reduction in security events and incidents, demonstrating the effectiveness of the security controls.

It’s worth noting that had the network been more actively used by regular users, we might have seen a higher volume of security events and alerts in the 24 hours following the hardening process.
