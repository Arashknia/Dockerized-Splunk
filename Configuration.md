ok now if you did all the sections of the [installation.md](https://github.com/Arashknia/Dockerized-Splunk/blob/main/Installation.md), it is time to configure our Splunk to get logs from our Windows device and send them to our Splunk container. so for that purpose, first we need some sort of things  which are mentioned below and then we will configure them:
---
  > Login with your account in the [Splunk base](https://splunkbase.splunk.com/) website and download [Splunk_TA_windows](https://splunkbase.splunk.com/app/742) and [Windows Event Log Analysis app](https://splunkbase.splunk.com/app/3067) and then unzip Splunk_TA_windows and Windows Event Log Analysis files:

  > Requirements for UF (universal forwarder):
  > - send_to_indexer add-on
  > - Spunk_TA_windows add-on

  > Requirements for Indexer:
  > - Listen 9997 add-on
  > - Spunk_TA_windows add-on
  > - index creation

  > Requirements for SH (Search Head):
  > - Spunk_TA_windows add-on 
  > - Windows Event Logs Analysis App
---

# Splunk_TA_Windows Editions and configurations:
---
1. inside Splunk_TA_windows create a folder with the name "local"
2. there is a file with the name "inputs.conf" inside the "default" folder in the Splunk_TA_windows. copy and paste the inputs.conf into your "local" folder that you have created.
3. open the inputs.conf in the "local" folder and edit the "Application - Security - System" parts like below:
```
###### OS Logs ######
[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
renderXml=false
index = wineventlog

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

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
renderXml=false
index = wineventlog
```

5. save and close the file
6. now put your edited Splunk_TA_windows into this directory: (SplunkUniversalForwarder Installation Path)\etc\apps

# Send_to_indexer Editions and configuration:
1. go to the directory (SplunkUniversalForwarder Installation Path)\etc\apps
2. create a folder with the name "Send_to_indexer"
3. 

# Listen 9997Editions and configuration
