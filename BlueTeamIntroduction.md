#Blue Team Introduction

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




