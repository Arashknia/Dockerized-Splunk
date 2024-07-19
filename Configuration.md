Ok now if you did all the sections of the [installation.md](https://github.com/Arashknia/Dockerized-Splunk/blob/main/Installation.md), it is time to configure our Splunk to get logs from our Windows device and send them to our Splunk container. so for that purpose, first we need some sort of things  which are mentioned below and then we will configure them:
---
  > Login with your account in the [Splunk base](https://splunkbase.splunk.com/) website and download [Splunk_TA_windows](https://splunkbase.splunk.com/app/742) and [Windows Event Log Analysis app](https://splunkbase.splunk.com/app/3067) and then unzip Splunk_TA_windows and Windows Event Log Analysis files:

  > Requirements for UF (universal forwarder):
  > - send_to_indexer add-on
  > - Spunk_TA_windows add-on

  > Requirements for Indexer:
  > - Listen_9997 add-on
  > - Spunk_TA_windows add-on
  > - index creation

  > Requirements for SH (Search Head):
  > - Spunk_TA_windows add-on 
  > - Windows Event Logs Analysis App
---

# Splunk_TA_Windows Editions and configurations:
---
1. Inside Splunk_TA_windows create a folder with the name "local"
2. There is a file with the name "inputs.conf" inside the "default" folder in the Splunk_TA_windows. copy and paste the inputs.conf into your "local" folder that you have created.
3. Open the inputs.conf in the "local" folder and edit the "Application - Security - System" parts like below:
```
###### OS Logs ######
[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
renderXml=false
index = wineventlog
sourcetype = wineventlog

[WinEventLog://Security]
disabled = 0
start_from = oldest
current_only = 0
evt_resolve_ad_obj = 1
checkpointInterval = 5
blacklist1 = EventCode="4662" Message="Object Type:(?!\s*groupPolicyContainer)"
blacklist2 = EventCode="566" Message="Object Type:(?!\s*groupPolicyContainer)"
renderXml=false
index = wineventlog
sourcetype = wineventlog

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
renderXml=false
index = wineventlog
sourcetype = wineventlog
```
5. Save and close the file.
6. Now put your edited Splunk_TA_windows into this directory: (SplunkUniversalForwarder Installation Path)\etc\apps
---
# Send_to_indexer Editions and configuration:
1. go to the directory (SplunkUniversalForwarder Installation Path)\etc\apps
2. create a folder with the name "send_to_indexer"
3. create a folder with the name "local" inside the "send_to_indexer" folder
4. create a file with the name outputs.conf and edit it like below:
```
[tcpout]
defaultGroup = idx-group
```
6. Save and close the file.
---

Now it is the time for us to go to the inside of our Splunk container to config indexer and search head:
1. first let's start the splunk. for that, we need to go inside the container and there are two ways for us to do that.
   > - First way: run cmd as administrator and enter this command to execute the container and get bash environment
   >  ```
   > docker exec -it splunk bash
   >  ```
   >  - second way: go to your docker desktop and log in with your docker account on it (sign-up if you didn't have any account till now) > on the left tab click on containers > click on Exec on the top
   > be sure of Splunk status is on start and running. otherwise, none of these two ways will work.
