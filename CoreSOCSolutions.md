_____
#  Core SOC Solutions
_____
## Key Takeaways:
 - Understanding EDR fundamentals and dashboard/alert navigation
 - Understanding the importance/operation of SIEMs and how it is utilize to solve many problems in security visualization.
 - Working with and learning the Splunk dashboard, and how to use it to query log data
 - Working with and learning the ELK stack to understand how to use it to query log data.
 - Understanding SOAR operations, and some of the issues they address within a SOC
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

Elastic Stack (ELK) is a collection of various open-source tools that work together to collect, analyze, and visualize logs. This is also the core foundation behind the Security Onion operating system.

ELK is made up of 4 components:

1. Elastic Search - A search and analytics engine to store and analyze data.
2. Logstash - A data processing enginer that pulls data from different local log sources, filters, then normalizes it to send to it's destination.
3. Beats - A host-based agent that ships data to Elasticsearch from the endpoint
4. Kibana - A web-based log visualization tool that works alongside Elasticsearch to create dashboards and view data with intent.

They work together in a general flow, as such:

Beats (Collects data) > Logstash (Data input) > Elasticsearch (Index and store data) <> Kibana (Analyze and visualize data)

### ELK Lab

In the discover page, where most analysts work out of, there are various tabs to utilize:

- Logs - Shows a single log containing event information with fields and values within that log
- Fields Pane - Shows the list of fields parsed from the log. Can be used to filter with
- Index Pattern - Each type of log is stored in a different index pattern. We can filter the logs via the selected index pattern
- Search bar - where the user can query logs based on what they are looking for
- Time Filter - Filter based on date/time
- Time Interval - Chart that shows the event counts over time
- TOP Bar - Contains options to save/open saved searches
- Discover Tab - The main workspace for Kibana to analys daata
- Add Filter - Apply filters to narrow results easily

My lab provides me with a running instance of ELK at 10.64.168.136.
I connect to the server and go to the discover tab. I was initially not seeing any logs, but I realized by default I was only searching for the last 15 minutes. I changed that to go a few years back and could now see all of my logs:

<img width="1218" height="850" alt="image" src="https://github.com/user-attachments/assets/9d1dbb00-5fe2-4c93-a5ca-4a4a0266e183" />

Now I've got some questions to answer:

1. Select the index vpn_connections and filter from 31st December 2021 to 2nd Feb 2022. How many hits are returned?

Simply adjusting my time range from what I did above to this date range provided the same answer, 2861.

2. Which IP address has the maximum number of connections?

By simply opening the Source_ip field on the left side I could see that the IP address with the most connections was 238.163.231.224

<img width="1217" height="758" alt="image" src="https://github.com/user-attachments/assets/2057cf96-4c3d-4636-bdad-e3f865e711ca" />

3. Which user is responsible for the overall maximum traffic?

At 4.0% of traffic, I can see that James is the user with the most traffic when looking at the UserName field info.

<img width="1207" height="754" alt="image" src="https://github.com/user-attachments/assets/3808f56d-3b1d-45d5-8c05-3c6d885b78fb" />

4. Apply Filter on UserName Emanda; which SourceIP has max hits?

To find this, I first needed to sort results by logs only where Emanda is in the UserName field. I did this by clicking the + sign next to the field data. 

<img width="1224" height="741" alt="image" src="https://github.com/user-attachments/assets/13afce8c-1015-4b66-8c1c-3ad69c8b780d" />

I could then check the Source_ip field to see what IP had the most hits, being 107.14.1.247

5. On 11th Jan, which IP caused the spike observed in the time chart?

By clicking "Visualize" on the time stamp field, I could select the spike Jan 11. data. From here, I could check the source IP field to see which IP had the most data.

<img width="1221" height="790" alt="image" src="https://github.com/user-attachments/assets/c6bb949e-1ad2-4b86-a91c-b776ef11b3cf" />

In this case, it was 172.201.60.191

6. How many connections were observed from IP 238.163.231.224, excluding the New York state?

For this, I could simply filter for only 238.163.231.224 as the Source_ip, and use the - button the exclude anything from New York under the source_state field.

<img width="1224" height="755" alt="image" src="https://github.com/user-attachments/assets/04760cd8-7bec-4769-b945-8e666c218dad" />

My answer was 48 connections, as seen by the 48 hits at the top once filtered.


### Kibana Query Language (KQL)

Kibana Query Language is a searching tool used to search for data within your log files.

It is important to note that a search for "United States" will return results for "United States", but "United" will not. This is because KQL expects the entire term. To combat this, you can use the wildcard symbol \* to accomplish this. For example, United* will return results for "United States"

Logical Operators - You can use the AND, NOT, and OR operators to search for specifics. For example Source_ip : 1.2.3.4 NOT UserName : Dylan
This will filter for anything from the source IP address 1.2.3.4 but will exclude log from the username Dylan. This can be done similarly for the AND and OR operators.

Field Based Search - As seen above, you can search for fields, not just key terms. You can use something like:
source_country : United States
to filter for anything originating from the United States.

For my lab question, I was asked:

Create a search query to filter the logs where Source_Country is the United States and show logs from User James or Albert. How many records were returned?

All I needed to do was create this string to include the following information, and it returned 161 results.
Source_Country : "United States" AND UserName : "James" OR UserName : "Albert" 

<img width="1222" height="750" alt="image" src="https://github.com/user-attachments/assets/4d0d7e15-9ec0-4008-b1f0-90e8997ed339" />

Next question:

A user Johny Brown was terminated on the 1st of January, 2022. Create a search query to determine how many times a VPN connection was observed after his termination

It took a few tries and some research before correctly entering the syntax KQL expects for the date, but eventually got it, and used the AND operator to specify the user:
@timestamp > 2022-01-01 AND UserName : "Johny Brown" 

<img width="1219" height="759" alt="image" src="https://github.com/user-attachments/assets/0a788828-16f8-44ad-b103-ec48bb8f2a28" />

My result here was 1.

## Visualization

Next I can make a new visualization to answer a few questions. Visualization is used to create graphs and charts to provide insights and better understanding of various logs/field data.

<img width="1219" height="760" alt="image" src="https://github.com/user-attachments/assets/87d25f46-7096-4f31-99f0-5b7c6cbfb0c5" />

After adding the Action : failed filter to my filter, I created a dsahboard out of it.

<img width="1223" height="752" alt="image" src="https://github.com/user-attachments/assets/0a41fdf1-ce41-49e1-8ac3-36c4bd7f6da6" />

I can now use this to answer my questions:

1. Which user was observed with the greatest number of failed attempts?

I could do the same thing I did above, and additionally filter by the Username field:

<img width="1224" height="755" alt="image" src="https://github.com/user-attachments/assets/fa1932a3-58e1-467e-9d00-25601c014101" />

My answer here was Simon.

2. How many wrong VPN connection attempts were observed in January?

Now, I just needed to change the time stamp on my filter for only days within January:

<img width="1217" height="747" alt="image" src="https://github.com/user-attachments/assets/2581f9b9-2e2c-43e3-931a-eb7cde6cbb3d" />

When hovering over the graph, I could see my answer was 274.

_____

## Introduction to SOAR

____

### What is SOAR?

SOAR Stands for Security Orchestration, Automation, and Response. It's meant to automate and streamline security management through various workflows and tools, to aleviate some of the common issues SOCs face like:
 - Alert Fatigue
 - Disconnected tools (endpoint agent services shutting off, or log connectors getting broken)
 - Manual Processes eating up time
 - Skills shortage

The main uses for SOAR are as follows:

1. Orchestration - There are many tools, platforms, and vendors utilized day to day by a SOC analyst when investigating alerts. Navigating all of these at once can be cumbersome. The point of orchestration is to use the SOAR platform to incorporate all of these tools together into one toolbox to take action based on playbooks, or assist analysts in triage and remediation.

2. Automation - This is using the SOAR platform to create automations based on certain alert triggers. If a certain incident/circumstance occurs, the SOAR platform will initiate and script that takes relevant action as a result.

3. Response - Using playbooks and automation, the SOAR platform can take necessary actions to resolve an incident, or at least mitigate it, when it first comes in. That being said, SOAR is not a one-size-fits-all approach, and is more used as a tool for SOC analysts to better understand and address various tasks/alerts to imrpove their workflow and efficiency.

Below are some pictures of provided playbooks to analyze:

<img width="913" height="798" alt="image" src="https://github.com/user-attachments/assets/855919d4-64af-4577-a64e-a0f07585b661" />

<img width="1858" height="610" alt="image" src="https://github.com/user-attachments/assets/3e1fa2fa-d1c3-4b6c-96da-80c40af3df6f" />

As we can see in both, there is manual input for a SOC analyst, as crucial decision making and verification is still required by human touch. That being said, most of the work is done by the SOAR tool. In both options, there is a clear playbook configured that gives yes or no options based on the circumstnace, and takes action down that path accordingly. This process cuts down a lot of the fat that would originally take up the analysts time, like checking attachment hashes againts threat intelligence, or applying patches through a PM platform.

Though it is not mentioned here, I do want to point out that it is important for Junior through Senior analysts to have a good understanding of these playbooks and what they are doing. Solely relying on the SOAR tool creates a point of failure in an organization. Analysts musts thoroughly understand the process start to finish, so they can understand the context and information provided by the SOAR once it runs through its processes.

_____

<img width="722" height="462" alt="image" src="https://github.com/user-attachments/assets/c7068fff-b1ed-441f-b7d7-7dcf73a05d2f" />

______

<img width="706" height="364" alt="image" src="https://github.com/user-attachments/assets/b2d265d4-710c-495f-85f2-e0599221e2ec" />

______

<img width="701" height="362" alt="image" src="https://github.com/user-attachments/assets/ca7e818d-d4f2-4012-9bb3-2648df7b7812" />

______

<img width="702" height="366" alt="image" src="https://github.com/user-attachments/assets/10e8304a-5fd0-42b4-b391-2a97092b3730" />

______

<img width="706" height="467" alt="image" src="https://github.com/user-attachments/assets/e53abc96-7fe7-4ab1-b4e2-b562a024bae8" />

______

Reasoning - A lot of these were pretty straight forward. It makes sense to automate tings like threat intelligence, ticket creation and communication, blocking malicious IPs/domains, etc. as they do not require immediate human decision. It is the analyst validation, testing, and confirmation that cannot be automated. It is important that a human eye looks this over to ensure results and actions make sense for the client (or head) organization, the SOC, compliance, future security posture, long term resoltuion of the event, and more.
