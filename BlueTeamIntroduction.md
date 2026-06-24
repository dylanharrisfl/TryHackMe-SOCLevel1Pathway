# Blue Team Introduction
_____
The goal of this first module is to understand the basics of SOC/security analyst roles, as well as the fundamentals of triage, escalations, social engineering, and system attacks.
_____
## Key Takeaways

 - Learned more about the basic roles within a SOC/security environment
 - Learned more about SIEM dashboard and escalation fundamentals
 - Reviewed common attack vectors through social engineering and system vulnerabilities
 - Explored more on how to enforce policies to improve security posture based on security incidents/events after remediation.
____

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

The next module here highlights the vulnerability of humans. People are the perfect attack vector as they tend to already have access to the resources you need. That being said, why try to pull of an impossible breach, when you can just run a social engineering campaign and harvest the credentials of someone with prexisting permissions to sensitive resources?

Below are some examples of social engineering attacks on people:

 - Phishing attacks - These are attacks centered around delivering communication over commonly used mediums (like email or SMS) to gain a sense of trust, and incite an emotional response from the target (fear, urgency, etc.). By getting the victim to engage in the attack, threat actors can deliver payloads, harvest credentials, redirect to malicious websites, etc.
 - Malware Downloads - Using things like adware, malicious QR codes, website redirection, etc., attackers can trick victims into clicking on, downloading, and running malcious packages on their workstations. These typically pose as legitimate downloads or links to convice users to engage with them.
 - Deepfakes - With the rise of AI, we are starting to see a lot of attackers utilizing tools like voice deepfakes and video deepfakes to trick employees or individuals into engaging in an attack. Typically, these deepfakes are impersonating a trusted superior or coworker to gain a sense of trust.

So what can be done to defend against these attacks?

 - Anti-phishing solution - While TryHackMe defines this as a solution to quarantine phishing emails, I believe a more ideal toolset would be an email threat protection suite. This tool should be able to not only remove phishing emails, but also quarantine/block and spoofed email, malicious links/images/attachments, or emails domains impersonating an organization with a legitimate email domain (for example tryhackne.com inestead of tryhackme.com).
 - Antivirus/EDR - This tool can prevent against malicious packages that does get delivered to the endpoint. It acts as a secondary line of defense to protect against anything that makes it past the user awareness stage.
 - Principles/Security Awareness Training - Running fake phishing campaigns, training, and deploying security pricniples are great ways to make your staff more aware of how to protect themselves against these attacks. Something I like to do personally to enforce this is to have users complete SAT training sessions, and those who continue to not participate will be locked out of connecting to a (non-vital to workflow) resource until they complete training. This will cause enough of a nuissance to those who struggle to participate to encourage them to complete training on time.

Next, we have a lab. I am greeted with a security dashboard and some tasks to complete:

<img width="1204" height="325" alt="image" src="https://github.com/user-attachments/assets/0c1b14e3-3b00-479f-a9cc-918b2c67ca5d" />

Here is my first Employees at risk case to investigate and respond to:

<img width="685" height="527" alt="image" src="https://github.com/user-attachments/assets/977594ae-7f68-4874-ba26-1e260f9984c1" />

I get these messages/questions/EDR alerts every now and then from clients. Seeing that this package is not received from the original vendor, and is not passing EDR checks, it is vital to avoid this application. There are many issues that could be at play here. For example, there may be a supply chain attack where attackers took advantage of the freeware sight and deployed C2 installers within the repositories of the software installers they have posted. In this case, we will quarantine this installer and source the original installation package for 7zip.

Next case, a SIEM alert:

<img width="665" height="404" alt="image" src="https://github.com/user-attachments/assets/ac0d98aa-fb3c-424a-af36-be16ea14b121" />

This is a clear cut phishing attempt. There is a noticable sum of money involved, causing urgency for the reciever. This also contains a .rar package for what would typically be a .pdf for an invoice. Finally, we see the email originates from a stripe-payments.xyz domain, but the email body says to email support@stripe.com. It is worth noting that the discrepency is moderately alarming, but not always fully indicative of phishing. In this case we will block the email and begin further analysis.

Next case, a support request from a client/coworker:

<img width="674" height="405" alt="image" src="https://github.com/user-attachments/assets/d00c41ff-9179-4a0a-a5f7-8aa1fb66c6c7" />

This is an example of a deepfake attack. Someone simulated a position of power, during non business hours (can sometimes be interpreted as priority/urgent), from an unknown number to harvest credentials. We will disable the Gmail account immediately and investigate further

Next case is a SIEM alert:

<img width="665" height="462" alt="image" src="https://github.com/user-attachments/assets/83971f94-4c1b-4ce8-a3d0-ed0f2bf9a97c" />

This is a suspicious login location alert. While Rose may very well be operating from a different remote location, it is not expected and should be addressed carefully. We must disable the account and verify with Rose whether this is intentional.

For the next challenge, I need to now deploy security policies to improve our current standings based on what we have experienced from the recent issues:

<img width="925" height="628" alt="image" src="https://github.com/user-attachments/assets/98fba046-cf2c-4c4d-812d-50e63953660d" />

Here are my choices:

<img width="907" height="678" alt="image" src="https://github.com/user-attachments/assets/74d69ea6-dd2b-4dbf-8060-ce249c5a4b2c" />

Explainations:

1. Anti-Phishing Solution - This will help defend against some of the phishing attacks like the one received in the second case.
2. Access Management Policy - This will clearly identify a safe and standard policy on how to go about password resets to better defend against deepfake/social engineer attacks like the one in the third case.
3. Antivirus Solution - This will improve endpoint protection when users unintentionally run malcious programs on their workstations, like in case 1.
4. Security Awareness Program - This will improve overall user awareness on how to protect themselves against social engineering attacks like the ones they experienced in cases 1-3.

_____
# Systems as Attack Vectors
_____

Next, it's important to discuss that systems can be attack vectors without human interactions. If a relevant system does not have necessary protections in place, an attack can very easily exploit it without victim knowledge/participation.

Below are some examples of system attacks:

- Human-Led attacks - These are attacks that are initiated by a system user in some way. For example, poor system configuration, weak passwords, or plugging in a malicious USB stick found outside the office. This human errors leave systems vulnerable for attacks.
- Vulnerabilities - Configuration flaws in softwares are beyond common. This leaves them vulnerable to exploits. That is why having strong patch management policies and verification processes are vital to protect against known and patched issues. That being said, zero-day attacks still present a serious threat, so having the necessary measures to cover for the unknown vulnerabilities will allow you to protect against a zero-day attack if it is deployed. For example, deploying proper access controls like RBAC or MAC to ensure resources are only access as needed or based solely on if the role authorizes that user to access it.
- Supply Chain - Software suppliers can have their tools hijacked to deployed malware within them. When the known-safe program is run, it would then additionally install the attackers payload as well to all customers. For example, if an RMM tool is attacked, users that are installing new RMM agents now have a malcious payload installed on their computer.

Below are some more policies I selected to best improve security posture:

<img width="822" height="718" alt="image" src="https://github.com/user-attachments/assets/333da250-c0eb-4099-95d1-40708b7db676" />

Explainations:

1. Patch Management Policy - A good patch/vulnerability management process/tool allows a security team to remediate vulnerabilites as patches come out for them, as well as improving security insights within the network.
2. Secure Password Policy - Enforcing complex and rotation password removes the vulnerability of weak and stale passwords as a whole. A chain is only as strong as it's weakest link, so a weak password could very easily take down an well-secured network. This will encourage users to utilize strong passwords to protect their accounts.
3. Antivirus Protection - Having endpoint security adds another layer of protection onto a workstation. If a threat gets past the human eye and is able to execute something malicious, antivirus can act as another line of defense to catch and quarantine the threat before it causes damage.
4. Security Training for IT - Requiring users to go through regular security training, especially for IT professionals (who are commonly working with critical systems) improves on the chance of misconfiguration. For exmaple, we can make junior technicians aware of the vulnerability that default passwords on network devices create.

_________


