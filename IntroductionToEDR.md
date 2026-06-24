_____
#  Introduction to EDR
_____
## Key Takeaways:
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
