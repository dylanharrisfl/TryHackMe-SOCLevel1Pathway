____
# SOC Team Internals
___
Explore relevant SOC concepts like triage and escalation of high-risk alerts, as well as key metrics tracked in SOC environments.
___
## Key Takeaways
 - Learned more about successful triage and escalation practices
 - Learned about work/playbooks and how to create/utilize them effectively
 - Explored key performance indicator metrics, how they are calaculated, evaluated, and used to improve SOC team performance accordingly.
____

### Events and Alerts

It is important to create the distinction between events and alerts.

SIEMs and other log injestion toolsets received thousands-millions of logs everyday. An event is simply something that occurs; a successful login, a file creation, a remote session initiated, etc. An event then creates a log and delivers it to your log injestion tool. From there, your security stack/SIEM should be able to look into each log, and when it noticies suspicious file hashes, IPs, patterns, etc., it will then create an alerts.

Those alerts are what security analysts are reviewing and addressing everyday, so training these alerts to be as accurate as possible is a must to save time and resources (while still catching everything malicious)

It is the role of the L1 analysts to distinguish between true vs false positive alerts and hand up to L2 analysts if they are malicious. This is where escalation takes place.

It is the role of the L2 analysts to receive the escalations and engage in a deeper analysis and remediate threats if needed.

____

### Alert Properties

Below are the various properties/fields that typically come within alerts:

 - Alert Time - Shows the time the alert was created (typically triggers shortly after the event occurred)
 - Alert Name - Brief summary based on the detection (Later Movement, Suspicious Powershell Execution, etc.)
 - Alert Severity - Defines the urgency of the alert. This is typically set upon detection, but can be edited by analysts during triage if needed
 - Alert Status - Information regarding the status of the alert (who is working on it, and what stage of review they are in)
 - Alert Verdict - Defines whether the alert was false or true positive. In my experience, within SentinelOne for example, there are specific true/false positive variation to categorize them based on what the analyst determines the alert is. This provides more information on the verdict rather than true/false.
 - Alert Assignee - Shows the assigned analyst(s)
 - Alert Description - Explains various information about the alert.
 - Alert Fields - Various fields regarding specifics of the alert (affected IP address, executed command, parent process, etc.)

In this module we are also asked to look into a few alerts in the model SIEM dashboard:

<img width="1492" height="665" alt="image" src="https://github.com/user-attachments/assets/ec13686e-cf1f-4c90-92d5-e7d201475ae2" />

What was the verdict for the "Unusual VPN Login Location" alert?

 - The verdict is marked as false positive. We also have a note explaining that the user was intentionally signing in from another location utilizing a VPN.

What user was mentioned in the "Unusual VPN Login Location" alert?

 - We can see here the user is stated as "M.Clark"


It is also important to cover alert prioritzation. From these alerts, what do you prioritize?

First, make sure you need to filter out anything uncessary. For example, alets that have already been resolved.
Next, make sure you filter by severity and time. Prioritize the most severe, and the oldest alerts first.

I'm asked to look at the board and see what we should prioritize first:

<img width="1469" height="622" alt="image" src="https://github.com/user-attachments/assets/35893b6a-b0e9-47c7-8809-a5389f190dd5" />

Given the criticality of the alert, the "Potential Data Exfiltration" alert takes precedence over the low priority "Download From GitHub Repository" alert.

___ 

### Alert Triage

TryHackMe provides a flow chart of how to follow alert review/escalations.
The main idea is:
 - Prioritze and select alerts and begin investigation
 - If there is a pre-built workbook regarding this alert, follow that. If not, utilize your SIEM resources to investigate manually
 - If you cannot reach a timely conclusion, escalate to L2
 - If you can, remediate/resolve the issue, document your findings, and close the alert.
 - Move onto the next

Workbooks explained - Workbooks (sometimes called play/runbooks) are pre-built guidlines on how to approach a specific alert category. The give key recommendations on how to get started or resolve a certain incident based on the alert itself.

If you don't have a workbook for an alert, here are some general guidelines:
1. Note the victim of the attack (affected user, hostname, IP address, network, application, etc.).
2. Review and understand the actual action performed in the alert.
3. Review relevant/related processes or events surroundiung the alert. This can help put together a timeline of events and conclude a true/false positive verdict.
4. Use threat intelligence tools like VirusTotal to verify your findings.

Triage Practice - 

I practiced triaging a few alerts. Here is an example:

<img width="1476" height="684" alt="image" src="https://github.com/user-attachments/assets/54aa26d7-cbf2-4221-95f9-f55d038b8f35" />

I first put the ticket in progress and assigned myself so other analysts know I'm actively working on this threat. After reviewing, I can see a suspicious .mp4.exe executable file from a suspicious website.
To me, this already looks like a True Positive
I decided to check the MD5 hash provided against VirusTotal and found that this is recognized as malware by most security providers listed.

<img width="1893" height="842" alt="image" src="https://github.com/user-attachments/assets/5da48318-176b-4226-989c-dd072043e72c" />

I marked the alert as true positive, and will plan to remediate the threat immediately.

____

## Alert Reporting and Escalations

___

When escalating to level 2 analysts, its vital to communicate effectively within the alert itself for many reasons:

 - Provides context for L2 Analysts so they don't have to triage themselves. It's important to provide them what you have found already, as well as why the ticket was escalated so they can begin ASAP. I always appreciate my junior technicians spending the time to explain to me in an alert/ticket WHY they needed to escalate the alert, and what has been done so far. This helps me circle back with them later to improve their understanding on what I did to resolve the alert.
 - Documentation - Having clear-cut notes allows for effective future reference if needed. This also allows the SOC manager or engineer to categorize and track metrics of recent alerts, and consider things like "what's the L1 team currently struggling to remediate?"
 - Imrpove investigation skills - Being able to explain your findings forces L1 technicians to learn more about what they are actually working on. It's the same idea as being able to teach something is the best way to show you know the material. It encourages L1 analysts to learn and continue to improve as they get stuck and need to escalate.

Reporting Format - Here is a good standard for formatting a report to make it understandable by your escalation team:
 - Who - Who is the user doing the action?
 - What - What was performed?
 - When - When did the activity occur/end
 - Where - Which device/network/application did the event occur on (Where within the infrastrcuture?)
 - Why - The reasoning for your final verdict

The Model SOC dashboard provided me with some alerts to investigate. They asked me to find some 5W information on these alerts like the affected user in the "Sensitive Documents Shared to External", and the sender email address in the "Email Marked as Phishing after Delivery" alert. 

<img width="1830" height="881" alt="image" src="https://github.com/user-attachments/assets/07a2c15a-30f3-4b78-b858-836acf36b3b2" />

I was then asked to triage the Phishing alert. I marked it as true positive with the following reporting:

Who - Email received by Eddie Huffman
What - A REPORT.rar was delivered from support@microsoft.com. The email did not pass SPF or DKIM checks
When - The alert occured at 19:25 on May 27, 2025
Where - Received by the email server, caught by automated analysis post-reception
Why - This is marked as a true positive given the supsicious .rar package attached, the failed DKIM/SPF checks, and the clear intentional spoofing of a trusted vendor, Microsoft, regarding payment information.

_____

Alert Escalation:

It is important to know how to escalate alerts to L2.
When doing so, make sure you communicate effectively with your L2 letting them know you are escalating an alert to them. 
If you don't fully need to escalate to them, it is also advantageous for both parties if you communicate your struggles with the L2 so they can guide you in the right direction. This not only helps the L1 learn, but it takes some load off of the potentially busy L2, allowing them to be utilized elsewhere.

More about escalation communications - 

 - Ensure you have contacts like a L3 Aanlyst or SOC manager to contact in the case L2 is not available
 - Communicate with clients over mediums NOT involved in the breach, if internal communications are compromised
 - Ensure you effectively communicate your workload to your superiors if you are not able to handle the current load. This is important so all alerts are handled appropriately in a timely manner.

____

## SOC Workbooks and Lookups

____

Working in a SOC, primarily an MSSP with many different clients, it is difficult to know the relevancy of users based on just their name. That is why it's important to have well configured user accounts in various identity provides such as:
 - Active Directory
 - SSO Providers Like Okto or Google Workspace
 - HR systems
 - Custom CSVs

With large environments, it's also very helpful to have strong documentation of various assets using various tools. Without effective documentation or intuitive naming schemes, analysts will need to waste time figuring out what the device/server is before they can truly understand the threat in its entirety. Some tools to keep in mind for this:
 - Active directory (On prem, Entra ID, or hybrid synced)
 - SIEM/EDR solutions - These typically keep track of some asset information
 - MDM solution like MS intune
 - Custom Documentation - It is important to have accessible documentation that describes certain assets if they are not clear (for example, a Domain Controller will always be doing Active Directory services, but sometimes may host files or applications as well. If so, it is important to document this)

Another vital part of effective documentation is a network diagram. A comprehensive diagram built in a tool like Visio allows security and IT professionals to reference it to understand the logical layout of a network quickly. This should include IP addresses, open ports, servers, switches, where applcations hosted, subnets, routes, etc. 
This document should be very well secured, as depending on the depth of the document it can provide extensive information for an attacker to pivot/cause further damage with if they gain control of it.

<img width="960" height="386" alt="image" src="https://github.com/user-attachments/assets/44273764-2003-45d9-9c87-a5379d13e4cc" />

In this example we can see the various ports exposed to the internet from the firewall, as well as the internal networks within. 
It provides the CIDR notation network address of the database, VPN, and office network. If the attacker here has compromised a system on 172.16.15.99, we know that this system is living on the database network, so triage should start there.
_____

### Workbooks

Here is an example of a workbook for Unusual Login Location

<img width="1202" height="654" alt="image" src="https://github.com/user-attachments/assets/03ba2d88-ba9f-4e5c-b242-6fc3e25e0295" />

This describes the various paths to follow when investigating an unusual login location. Here's how I'd explain the stages:
 - Enrichment Stage - Use threat intelligence and alert info to gain information regarding the user and the incident
 - Investigation - Use SIEM logs and other information gather to make a verdict based on your findings
 - Escalation - Escalate if needed

The module provides a lab where I can create a model workbook:

<img width="1186" height="809" alt="image" src="https://github.com/user-attachments/assets/ef9142fd-d6ad-450c-839a-f907db0d42a4" />

Here is my final product:

<img width="1147" height="799" alt="image" src="https://github.com/user-attachments/assets/abe31046-97d2-49c2-9c5d-66e6e8d522cc" />

All of this follows the basic triage for suspicious email investigations
 1. First, we need to assign the ticket and being our investigation appropriately
 2. Next, analyze the email header information to gain more information about the SPF/DKIM tests results, the sender reputation, content, origin server, etc.
 3. Continue to manually sandboxing the attached content using tools like sysinternals to investigate how it affects the system and determine any clear IoA's
 4. Make your determination - Document accordingly
 5. Close as a false positive, or gather information for effective escalation and alert reporting then escalate.

____

### SOC Metrics and Objectives

_____

 - Discover concepts of SLA, MTTD, MTTA, MTTR.
 - Understand the importance of the False Positive Rate
 - Learn how to improve metrics as a L1 analyst

______

First, lets dive into some metrics and the formulas used to calculate them:

1. Alerts Count (AC) = Total Count of Alerts
2. False Positive Rate (FPR) = False Positives / Total Alerts
3. Alert Escalation rate (AER) = Escalated Alerts / Total Alerts
4. Threat Detection Rate (TDR) = Detected Threats / Total Threats

TryHackMe states that 5 to 30 alerts per day per L1 analyst is a good metric. It's important to not have too little, nor too many alerts to handle. In both of those extremes, there are real alerts being missed due to SIEM alerting misconfigurations/poor threat detection or sifting through too many false positives to handle the true positive threats.

A False Positive Rate of 80% or higher is cause for concern. This leads analysts to assuming most alerts are going to be false positives, and making this mistake on a true positive.

Alert Escalation Rate - 
An indicator of a strong L1 team is ones who's escalation rate is low. This indicates they are more independent, and able to handle most alerts without needing to escalate as much. A decent number to shoot for is below 50%, but the goal would be below 20%.

Threat Detection Rate - 
Threat detection rate should always be 100%. The simplest alert missed could result in devestating affects if it goes unrecognized and unchecked. It is vital for analyst to be thorough, and logging tools to be accurate and available at all times.

Next we have some metrics typically agreed upon between a SOC and the company management/client through their SLA (service level agreement)
 - SOC availability - Typically 24/7 or 8-5 M-F
 - MTTD - Mean time to detect - Typically 5 minutes - Mean time for the SOC tools to detect the threat
 - MTTA - Mean Time to Acknowledge - Typically 10 minutes - Mean time for the SOC to acknoledge the alert
 - MTTR - Mean Time to Respond - Typically 60 minutes - Mean time for the SOC to stop the spread of the attack

It is important to uphold the standards of these metrics for internal performance, as well as meeting the standards agreed upon/sold within the SLA.

## Improving Metrics

It's important to keep up with the metrics you and your team deliver, so you can focus and weak points and improve accordingly.

Here are some common metric issues, and some recommendations to solve them:

1. FPR over 80% - Exclude noise! Stop alerting on system updates and tune your tools to what really matters. Consider using SOAR/scripting to handle common alerts if appropriate
2. MTTD over 30 minutes - Get with the SOC engineer and consider resource usage and SIEM configuration to ensure log collection and injestion is happening efficiently at peak hours
3. MTTA over 30 minutes - Ensure analysts are aware every time a new alert comes in, and ensure staffing is sufficient to handle the current workload
4. MTTR over 4 hours - Ensure strong documentation, and ensure L2 analysts have the appropriately amount of time to dedicate to handling advanced threats efficiently and in a timely manner.

SOC Manager Labs:

The module presents a few various scenarios where I need to think like a SOC manager to improve my teams performance based on the metric affected:

Case 1:

<img width="1174" height="574" alt="image" src="https://github.com/user-attachments/assets/37ad02b9-6c0c-4341-8d00-b2bff6cc7631" />

Clearly, my team took too long to respond to this threat. Had the appropriate documentation been in place as a workbook, my L1 team could have handled this alert instead of escalating it to a L2 who was already busy that day.

Case 2:

<img width="1181" height="549" alt="image" src="https://github.com/user-attachments/assets/3630627b-68e1-4851-9cd9-a5daba3062c8" />

Here we have a 20 minute delay with MTTD. Our SIEM tools are not detecting alerts or injesting logs fast enough. In this circumstance, I need to assign my SOC engineer to tweaking our tools to imrpove detection time.

Case 3:

<img width="1177" height="582" alt="image" src="https://github.com/user-attachments/assets/23a305f0-5fe4-49c9-ae1b-02881b18ae4e" />

In this case we have a 95% FPR, meaning L1 analysts are spending way too much time investigating unecessary alerts. In this case, we want to investigate what can be excluded from alerting safely, and assign the SOC engineer to configure our tools to deploy said exclusion/adjustments.

______
