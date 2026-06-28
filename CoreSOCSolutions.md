_____
#  Core SOC Solutions
_____
## Key Takeaways:
 - Understanding EDR fundamentals and dashboard/alert navigation
Lab still in progress...
____

### What is EDR?

EDR (endpoint detection and response) is a security solution for deep endpoint protection. It is a tool that constantly reviews and monitors system processes and detect/address/alert threats as they occur.

Here are some common EDR solutions:
 - CrowdStrike Falcon
 - SentinelOne ActiveEDR
 - MIcrosoft Defender for Endpoint
 - OpenEDR
 - Symantec EDR

There are three main features of EDR to be aware of:
1. Visibility - This is one of the EDR features that makes it unique from other endpoint security solutions. This is the process of collecting data from the endpoints like processes, registry modifications, file and folder info, user logons, etc. This allows analysts to see who process trees of an event, and create a timeline accordingly. It also provides historical data to go back and reference if needed.

Here is an example image taken from a CrowdStrike Falcon instance showing a process tree:

<img width="1898" height="588" alt="image" src="https://github.com/user-attachments/assets/d5cbf6a3-cd57-4ce8-95ba-c21ace893d0d" />

Here we can see, for example, that cmd.exe is the process that originated sc.exe.

2. Detection - This next EDR feature utilizes signature-based, behavior-based, etc. detections to within the one tool to recognize malicious processes, files, or patterns. It can also detect fileless activity residing in memory. The main idea is that analyst and EDR vendors can feed various IoC information to look for and compare events to, so the device can alert accurately and quickly.

As you can see below, EDRs also typically incorporate linkage to various Tactic via Techniques from the MIRTE ATT&CK framework to provide more insights on the attack. I use SentinelOne, which also does this well:

<img width="1892" height="728" alt="image" src="https://github.com/user-attachments/assets/ac2f1f00-18e6-4536-808a-e9aaf435f7f1" />


3. Response - EDR also provides analysts with the tools to take action based on threats. You can make changes on the endpoints, kill processes, isolate a device/device group, create automated responses to common threats, and more.


So then, whats the difference between AV and EDR?
This line tends to get blurred a little, especially in my early career before I began to research it more.
An AntiVirus is simply put just sees processes through a single pane of glass. It checks if that specific process is malicious against it's threat databases. If there nothing wrong, it moves on. EDR on the other hand logs everything and builds timelines and looks at process trees. If there is suspicious activity, even across a group of various events, it will alert/address the issue immediately. EDRs have a lot more insights, and a lot of a big-picture vision over the endpoint, making it the more ideal enterprise security tool.

Next, when EDR's generate alerts, it is the priority of the SOC analyst to triage them within the SIEM. EDRs are not the perfect standalone tool. They work best when working alonside other tools within a cybersecurity ecosystem. Thankfully, triage is made easier by the EDR as it automatically categorizes the alert on it's criticality, and provides most to all of the alert fields needed to being triage immediately.

EDR agents, also known as sensors, provide telemetry data on the endpoints they are installed on. All of this data can be viewed and categorized in the EDR console to provide insights to the security posture of your endpoints. Here is an example of a CrowdStrike EDR Console:

<img width="1757" height="820" alt="image" src="https://github.com/user-attachments/assets/52649a60-1dd5-4513-867d-dd0a40fed314" />

When I refer to telemetry, I am talking about the data the sensors actually pull from the endpoints. Here are some examples of what is actually pulled:

 - Process Executions and Terminations
 - Network Connections
 - Command Line Activity
 - Files and Folders Modifications
 - Registry Modifications

______

### Detection and Response

Next, we explore some of the various detection and reponse techniques utilized by EDRs:

Detections:

 - Behavioral Detection - It doesn't just match known threats like file hashes and IPs, but rather look for malicious behavior through a various string of events or processes. This 'behavior' is what the EDR catches and alerts on. For example "lateral movement". This is not necessarily a file hash, but rather a string of events that indicates lateral movement within a network.
 - Anomaly detection - When EDR collects telemetry data, it is building a baseline profile for the endpoint. When the tool sees your device significantly straying from your standard operation, the EDR will alert accordingly. For example, high outbound traffic on an endpoint device at 4 a.m. in a standard 8-5 M-F office. This would indicate data exfiltration and should be investigated.
 - IOC/MITRE ATT&CK Matching - EDR tools have links to many databases of threat intelligence. If they recongize activity that matches behavior of known IOCs, they will be flagged. This is further supported by specific mappings to the MITRE ATT&CK framework, like Persistence via Scheduled Tasks (Tactic via Technique)

As a result, we can build automated responses as incidents occur, tailored specifically to that event:

 - Isolate Host - If a threat is substantial, it is best practice to isolate the host in order to stop the spread as much as possible. Having automatic isolation, rather than waiting for a L1 SOC analyst to do so 10 minutes later can save an entire network from spread.
 - Terminate Process - This will kill malicious processes that may have kicked off on the endpoint. It is important to make sure this is accurate so you don't kill any business critical processes.
 - Quarantine - The EDR will terminate the threat, and quarantine it. This moves it to an isolated environment so it cannot be executed. Analysts can then review the process and remove/restore appropriately.
 - Pull forensic information - In the case that data needs to be extracted for forensics purposes, SOC engineers can configure automated responses to pull things like memory dumps, event logs, folder contents, registry hives, etc. when a specific event occurs.

Next, we have a EDR console model to do some lab work on:

<img width="1198" height="853" alt="image" src="https://github.com/user-attachments/assets/581b595f-7af7-484d-936d-8fa14f4bc426" />

First I poke around on this dashboard to see what I'm working with, the first thing hitting my eye is the brightly colored severities of the various alerts.

Next, I have some questions to answer:

1. Which tool was launched by CMD.exe to download the payload on DESKTOP-HR01?

To answer, I take a look at the alert for DESKTOP-HR01 and check the process info, as that will likely be where what CMD.exe had launched:

<img width="1178" height="865" alt="image" src="https://github.com/user-attachments/assets/f6658fb4-6dfa-4e5d-8cae-ab0e1c3a13bd" />

Here I can clearly see that CMD.exe is the parent process, which launched cURL.exe, a command line tool for communicating and receiving data from a server. From here, our EDR identifies this behavior as downloading the payload. So my answer here is cURL.exe.

The next question is:

2. What is the absolute path to the downloaded malware on the DESKTOP-HR01 machine?

We can look at the above picture here and see the absolute path used in the curl command to download the malware was C:\Users\Public\install.exe

The next question pertains to WIN-ENG-LAPTOP03, so I traverse to that alert to answer the following:

3. What is the absolute path to the suspicious syncsvc.exe on the WIN-ENG-LAPTOP03 machine?

I poke around on the alert to find the IOC information, and find the file path that launched the process:

<img width="1191" height="644" alt="image" src="https://github.com/user-attachments/assets/529c1cee-9160-4a9e-89bf-67346491dcd8" />

Under file path, I can see the answer is C:\Users\haris.khan\AppData\Local\Temp\syncsvc.exe

The next questions wants me to identify where the exfiltration attempt was made to.

4. On which URL was the exfiltration attempt being made on WIN-ENG-LAPTOP03?

I traversed to the proceess info below, and selected the syncsvc.exe process to actually see what it was doing:

<img width="1168" height="870" alt="image" src="https://github.com/user-attachments/assets/155333a5-d5f1-4279-85f3-3091820cb456" />

As you can see, the process has an alert for attempted exfiltration to https://files-wetransfer.com/upload/session/ab12cd34ef56/dump_2025.dmp

The final question regards the alert for DESKTOP-DEV01.

5. What was UpdateAgent.exe labelled by Threat Intel on DESKTOP-DEV01?

I searched the alert fields to find the threat intel infromation on the selected process:

<img width="1195" height="819" alt="image" src="https://github.com/user-attachments/assets/af6475fb-f8c9-4e53-8d41-e8a45a62d76a" />


This tells me here that it is a known internal IT utility tool, indicating it is likely a false positive. This should be confirmed before proceeding.

______

