_____
#  Core SOC Solutions
_____
## Key Takeaways:
 - Understanding EDR fundamentals and dashboard/alert navigation

Lab still in progress...
____

### Introduction to EDR?

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

## Introduction to SIEM
______

SIEM stands for Security Information and Event Management system. It is a tool utilized on an infrastructure to gather logs from all kinds of various devices on the network to injest, normalize, and alert accordingly. This one tools gives security analysts the ability to filter all logs originating from devices on the network in order to catch malicious traffic and address it immediately.

### Log source types - 

Log sources are typically chopped into two categories:

Host-Centric Log Sources - These logs sources capture event logs related to the host device. You typically see this as devices actually interfaced with a user, or something like a network server. They typically capture things like - 
 - A user accessing a file
 - A user successfully logging onto something
 - A user executing a command
 - A user adjusting a registry key value

Network-Centric Log Sources - These are logs generatic when communication occurs over a network. These are typically generated by things like firewalls, network taps, IDS/IPS, etc. Some examples of these logs would be:
 - Connection over port 22
 - A file accessed over SMB or FTP
 - A GET request sent to a website
 - VPN activity

While on their own, these logs can be useful, there are many challenges that arise as a result. For example, there are so many logs generated on a system at a time, it would be impossible for an individual to look through all of them manually. Also, there is no centralization, meaning the logs live solely on their originating system, limiting quick visibility. In addition to that, individual logs do not tell a whole story. Analysts need to put together a picture via different kinds of logs to put together a story (Malicious package install and ran on Windows Server, commands were ran, network POST traffic to a malicious IP address for exfiltration, etc.). Another issue would be formatting. Logs come from all different kinds of formats and languages, so it is important to normalize the data to make it understandable when combined together.

As a result, we utilize SIEMs. SIEMs can take care of all of these issues at once. Pointing log files to go to the SIEM so it can injest, understand, and filter for only relevant data. We can injest data from all different kinds of devices and log sources and normalize them to be one understandable format for analysis.

### Log locations - 

In Windows, logs are stored in the Event Viewer module. They are exported, typically in their proprietary format as .evtx files. Event viewer logs are what SIEMs injest to collect data on Windows endpoints.

In Linux, log files are stored in the /var/log/* directory. For example, /var/log/httpd contains information regarding web traffic, and /var/log/auth.log and /var/log/secure stores authentication logs.

Different SIEM tools have various log injestion methods like the syslog protocol, port forwarding, manual upload, or the Agent/Forwarder tool that is installed on endpoints to send logs to the SIEM server.

### How does detection work? - 

Alerts are created when specific logs meet certain criteria for pre-confirgured detection rules. For example: Endpoint sends x amount of GBs out within x amount of time during x hours of the night, alert X rule with X criticality. The SOC engineer can set these thresholds how they deem fit.

A detection rule is created as analysts/engineers see true positive attacks fit something often. If they can find common logs created during various types of attacks, we can create detection rules to alert when those events occur.

### SIEM Lab Work - 

I am presented a simulated SIEM evironemnts, with some questions attached.

First, the lab has me initiate suspicious activity to simulate actual malicious traffic occurring.

1. After clicking on the Start Suspicious Activity button, which process caused the alert?

<img width="713" height="645" alt="image" src="https://github.com/user-attachments/assets/31e84534-f3ef-45d4-9f6f-a9d6c44ac1c4" />

Immediately, I see the red cudominer.exe executable show up. I will investigate ASAP.

2. Find the event that caused the alert and identify the user responsible for the process execution.

After investigating the alert, I look around to find the process execution alert for cudominer.exe:

<img width="700" height="498" alt="image" src="https://github.com/user-attachments/assets/0513c522-f56d-4858-9b3d-c459003b3dd2" />

Right next to this excutable path is the user that initiated it, Chris.

3. What is the hostname of the supsect user?

Looking at the above image, we can see the hostname of the machine is HR_02.

4. Examine the rule and the suspicious process; which term matched the rule that caused the alert?

Once I selected the alert, I am greeted with the rule that caught the process:

<img width="706" height="340" alt="image" src="https://github.com/user-attachments/assets/67a43d8b-62d3-4d06-a6bf-ebe685726937" />

Here, there is a condition that alerts if the ProcessName = (\*miner* OR *crypt\*)
Given our process name is cudominer.exe, it would be miner that triggered the rule. Note: The "\*" in front and behind miner are wildcards, meaning anything can be there. In this case, as long as miner exists, no matter what surround it, this rule will trigger.

5. Finally, I mark the suspicious traffic as true positive and would move onto the next alert:

<img width="693" height="333" alt="image" src="https://github.com/user-attachments/assets/064ff070-26ae-4009-a9b5-5e6500378e14" />

_____

## Splunk Fundamentals

______

I started out by utilizing the Splunk VM provided free by the TryHackMe platform. They provide a VM I can use to access the Splunk interface at the provided IP.

This lab starts off by going over the 3 components of Splunk:

- **The Forwarder** is the agent-based utility that runs on all endpoint devices. It collects and forwards logs from the machine to the Splunk Indexer.
- **The Indexer** receives and processes data ingested by the forwarders. It formats data into key-value pairs and stores them to be queried later by the Search Head.
- **The Search Head** is where users can query the data within the indexer. It uses SPL (Search Processing Language) to search the data, which is then returned to the user in key-value pairs.

---

## Ingesting Data

The lab asks where we can ingest data from files and ports. By going to the Add Data tab in the dashboard, the "Monitor" tab is described as accomplishing this:

<img width="700" alt="Add Data Monitor tab" src="https://github.com/user-attachments/assets/b34a8594-9621-4817-9e7d-126aa6fa2b55" />

Next, I manually uploaded VPN log files as a log source to the Splunk interface. I went to Add Data and navigated to the upload option. There are five steps to follow:

**1. Select Source:** In this case, my VPNlogs.json file.

<img width="700" alt="Select source" src="https://github.com/user-attachments/assets/06454f78-f814-43a5-a554-e6da5e4f9a07" />

**2. Source Type:** Here I select the source type of the data, which auto-filled as JSON given the file type.

<img width="700" alt="Source type" src="https://github.com/user-attachments/assets/078c5a64-a0e8-4f28-b7b3-0f3d4c45fcdf" />

**3. Input Settings:** Here I can change which index these logs are dumped into, as well as a device name to associate with the logs.

<img width="700" alt="Input settings" src="https://github.com/user-attachments/assets/3848af4c-dda7-49d3-8c44-b4f8af4639d5" />

**4. Review:** Verify everything configured before proceeding.

<img width="700" alt="Review settings" src="https://github.com/user-attachments/assets/54e13f1c-e369-49a0-8f29-9c797282cddd" />

**5. Done:** From here I can start searching through logs, build dashboards, or add more data.

<img width="700" alt="Upload complete" src="https://github.com/user-attachments/assets/7abcdd54-a67b-476a-821a-51aca1cb01e7" />

---

## Lab Questions

**1. Upload the data and create an index "VPN_Logs". How many events are present in the log file?**

The lab asked me to create a new index, so the 5724 events I saw in the original "test_index" I had chosen weren't going to be the answer. I went back and created a new index in the Input Settings section of a new upload:

<img width="600" alt="Creating VPN_Logs index" src="https://github.com/user-attachments/assets/253b8ba0-063c-4de9-9c1c-e2b03a3d9745" />

<img width="500" alt="Event count result" src="https://github.com/user-attachments/assets/674f632e-ed1d-4203-9336-9aab8f9c4b48" />

> **Answer: 2862**

---

**2. How many log events are captured by the user Maleena?**

I took some time to poke around and explore the Splunk dashboard here. On the left I could see an "interesting fields" column with "UserName" as one of the options. After selecting it, it showed the top values used in these logs for the UserName field and Maleena had 60.

<img width="700" alt="UserName field values" src="https://github.com/user-attachments/assets/c40f4caa-7a14-401e-90be-95de46ba0504" />

One thing I noticed while exploring: there is an "a" or "#" next to each field indicating whether the value is a string or integer. A small touch that makes manual log investigation a little quicker and easier to understand at first glance!

> **Answer: 60**

---

**3. What is the username associated with IP 107.14.182.38?**

I first looked at the top 10 values associated with the Source_ip field but nothing was returned:

<img width="700" alt="Source IP top values empty" src="https://github.com/user-attachments/assets/8a846114-7f6d-40e3-bd5a-ebd5ca276422" />

Looking at the key-value pairs, I figured I just needed to query directly using the Source_ip field and the target IP. This wasn't my first try though. I learned that the syntax is case-sensitive, so `Source_IP` and `source_ip` weren't working. I had to match the exact spelling of `Source_ip` from the log. Once I did that, "Smith" came back as the answer in the first result.

<img width="500" alt="Smith result" src="https://github.com/user-attachments/assets/09e1641a-12a5-4316-b90c-2e5cd87df2b0" />

> **Answer: Smith**

---

**4. What is the number of events that originated from all countries except France?**

It would be silly to look at the 7 countries listed and write an operator listing only the 6 that aren't France. Instead I used the not equal operator (`!=`) to show any Source_Country not equal to France. Sure enough, this did the trick!

<img width="700" alt="Not equal France filter" src="https://github.com/user-attachments/assets/566b5454-a893-4513-8f33-eebf6a344588" />

<img width="700" alt="Result excluding France" src="https://github.com/user-attachments/assets/15dd8fa2-35de-4092-9dff-bea02d1a0a0e" />

> **Answer: 2814**

---

**5. How many VPN events were associated with the IP 107.3.206.58?**

This last question was pretty straightforward. I just needed to do the same thing as question 3 and query for any logs associated with the specified IP address.

<img width="700" alt="VPN events for IP" src="https://github.com/user-attachments/assets/7aeb10fe-e22f-49a8-ae08-49c43a0d5daf" />

> **Answer: 14**

_______

## Elastic Stack Fundamentals

______


