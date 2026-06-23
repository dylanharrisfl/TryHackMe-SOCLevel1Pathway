# Blue Team Introduction

This course starts out with a few basics, like introducing us to the various roles. 

For example, a Senior Analyst is used as a guidance for junior analysts when needed, as well as a point of escalation if needed.
Next, a SOC engineer, who's main priority is configuring/tweaking the SOC tools and procedures in order to make the analyst team more efficient in practice.
The SOC manager is the administration of the team, who communicates with lead management and all members of the SOC to ensure everything is running how it should be from a top level.
Finally, the incident responder, who's top purpose is to get involved in crisis' and resolve them quickly and accurately.

Next, I get introduced to a model SIEM, where a few things catch my eye immediately:

<img width="1191" height="837" alt="image" src="https://github.com/user-attachments/assets/e4db0307-026c-48de-8ce1-9b61dfde1f77" />

The first things that catch my eye are the bright red, critical alerts. I can see that there is a suspicious IP address trying to access SSH/port 22 from IP address 221.181.185.159.
I can also see various tabs like IP Hunter, Escalation, Firewall, and, of course, SIEM Dashboard.
I also see two charts at the bottom relating to Alert types and login country location. I feel this would be a great tool for an analyst to understand the current hot attack types, as well as indicate suspicious traffic (in the case of high login amounts from a non-expected country).

I place this IP address in the IP hunter to gather more information, similar to how one would utilize Virus Total to get more information on a potential threat source (like a hash, IP, hostname, URL, etc.)

<img width="823" height="724" alt="image" src="https://github.com/user-attachments/assets/9f799cad-f536-4f28-b0d8-d42827726fe7" />

I can see here that this IP is associated with the chinamobile.thm domain, originating from a China Mobile Communications ISP.
It also categorizes it as being know for: Port Scan, C2 Server, PlugX.
Port scanning is simply scanning attack surfaces for open ports and surfaces that can be utilized for exploiting.
A C2 server is a server utilized for Commands and Control over a victim system/network. It is the IP address/FQDN that infected devices will reach out to in order to receive malicious commands from the attack.
I had to research PlugX. PlugX is a remote access trojan that disguises itself as a legitimate program and deliver a payload through DLL-sideloading. Once in, it can be utilized as a backdoor into the system.

Next, I select who I want to execute this alert to, now that the threat has been identified.

<img width="809" height="427" alt="image" src="https://github.com/user-attachments/assets/685fb93f-8e8e-47b6-9036-4d3532b9dace" />

Will, being my SOC team lead, is the clear answer here.

Next, I need to deploy a simple firewall rule to block the malicious IP in order to slow down the attack:

<img width="1026" height="487" alt="image" src="https://github.com/user-attachments/assets/fe051829-f372-4eee-ab33-58d2995112cb" />

Here, I block the public IP address, as well as right a cohesive note identifying why. Though we may need to be quick, it is always important to add notes to important firewall changes so analysts in the future are aware of the rule's intention.

______________________________________________________
### SOC Roles in Blue Team
_______________________________________________________

In this next module, I review the various security roles within an organization.

At the top will be the CISO, who is utilized to manage multiple security teams/efforts in order to maintain and further the overall security posture of our organization.

On the Blue Team there are a wide range of different roles.
Within the SOC we see:
 - SOC Manager
 - SOC Engineer
 - SOC L1/L2 Analysts
This team primarily triage//investigate attacks/alerts as the come in. The SOC Engineer fine tunes these rules and the manager leads the effort and communicates with the senior management.

Next, is the Cybersecurity Incident Response Team, or CSIRT for short.
This team consists of professionals ready to be deployed at any moment to handle cyber breach incidents as they occur.
This contains roles like:
 - CSIRT Manager
 - Forensics Lead
 - Threat Intel Expert
 - Threat hunting Expert
 - Malware Analyst

Finally, there are some specialized roles within the cyber domain like the following:
 - DevSecOps
 - Penetration Tester
 - GRC Auditor
 - AppSec Engineer
 - Threat Analyst
 - AI Researcher
These roles provide unique and specialized skills to create a diverse team of individuals that can improve and protect different efforts within the organization in various ways.

This room also provides a lab to assign various people to certain scenarios based on there roles:

<img width="1080" height="749" alt="image" src="https://github.com/user-attachments/assets/2fa0d062-4caa-4da6-aa6f-ef9bab6660c7" />

Here is how I chose each role:

- SOC L1 Analyst - This role was chosen to investigate the firewall brute-force attempt alert, as this is preliminary attack triage. This is a low-risk incident, and does not pose any immediate threat. We should utilize Level 1 resources to investigate and remediate as needed.
- Soc L2 Analyst - I chose this role as there is a high-risk employee who has already launched a malicious program. We need to utilize Level 2 resources due to the high priority and severity of this incident
- CERT Lead - Infrastructure already being infected and encrypted with ransomware is means for immediate attention and remediation. Deploying a skilled team/individual trained specifically to investigate and handle incidents of this level is necessary.
- GRC Auditor - Because this involves PCI DSS compliance, it is important to have an individual trained and well-versed in GRC to audit and adjust the systems handling this information.
- Penetration Tester - To best understand if vulnerabilities are present on a system, it is ideal to deploy a red team individual to probe and test for vulnerabilities to see what can be identified and remediated.
- SOC Engineer - This individual is responsible for maintaining and improving SOC tools to keep systems healthy and ensure tools are alerting accurately. Because the SIEM tool is unavailable, it is the SOC Engineers priority to bring it back online so analysts can continue taking care of alerts.
- Threat Researcher - As malicious groups and threats emerge, develop, and adapt, it is ideal to have a threat researcher that can investigate the scope of these threats so the organization can premptively protect against it.

________________________________________________________
### Humans as Attack Vectors
_________________________________________________________
