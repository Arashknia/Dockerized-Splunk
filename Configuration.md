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
   >  - second way: go to your docker desktop and log in with your docker account on it (sign-up if you didn't have any account till now) > On the left tab click on containers > Click on your splunk container > click on Exec on the top
   > be sure of Splunk status is on start and running. otherwise, none of these two ways will work.
2. become root by entering ```sudo bash```
3. enter this command to see what is the status of Splunk:
   ```
   /opt/splunk/bin/splunk status
   ```
4. if it is running go to your browser and check the web UI on http://127.0.0.1:8000. enter your username and password!!! :/ and if it is not running run it with ```/opt/splunk/bin/splunk start```
   > HINT: your password is the password that you set when you were running the container by the command given to you in the [installation.md](https://github.com/Arashknia/Dockerized-Splunk/blob/main/Installation.md) part 8
6. ok now run this command to make the Splunk run on HTTPS protocol ```/opt/splunk/bin/splunk enable web-ssl``` (you may need to enter your username and password) and then restart the Splunk with ```/opt/splunk/bin/splunk start```. now the web UI should be visible on https://127.0.0.1:8000

# listen_9997 Editions and configuration:
1. inside your Splunk container enter this command to create the listen_9997 add-on to make the Splunk listen on port 9997
   ```
   mkdir -p /opt/splunk/etc/apps/listen9997/local/
   ```
2. now create the inputs.conf inside the "/opt/splunk/etc/apps/listen9997/local/"
   ```
   vi /opt/splunk/etc/apps/listen9997/local/inputs.conf
   ```
   > HINT: sorry, I don't teach you how to work with vi or vim and nano (just search for that please)
3. These are the configurations that should be put on the file:
   ```
   [splunktcp://9997]
   disabled = 0
   ```
4. save and close the file after editing
5. now you should put Splunk_TA_windows and Windows Event Logs Analysis Folders in "/opt/splunk/etc/apps/" with the docker desktop. for that just go to Docker desktop > Click on Containers on the left pannel > Click on your running Splunk container > click on Files (next to the Exec button) > find "opt > splunk > etc > apps" and right-click on "apps" > click on import > select your edited Splunk_TA_windows folder and do it for  Windows Event Logs Analysis too!
6. now run this command to give the Splunk user the necessary permissions:
   ```
   chown -R splunk:splunk /opt/splunk/*
   ```
7. restart the Splunk service:
   ```
   /opt/splunk/bin/splunk restart
   ```
8. After Splunk service restarted, go to web-UI > Settings > Forwarding and receiving > Configure receiving
   > You should be able to see 9997 under Listen on this port column and the status should be enabled
9. now go to the web-UI > Settings > Indexes > Click on the New Index on top right > Set the name of the new index with "wineventlog" > Save and create
10. edit _internal and _introspection size to only 50G. otherwise your host hard-disk with fill by them.
11. get back to your splunk container within Docker desktop and enter this command:
    ```
    cat /opt/splunk/etc/apps/search/local/indexes.conf
    ```
    > You should be able to see index of windows is created and is something like this:
    > ```
    > [wineventlog]
    > coldPath = $SPLUNK_DB/wineventlog/colddb
    > homePath = $SPLUNK_DB/wineventlog/db
    > maxTotalDataSizeMB = 512000
    > thawedPath = $SPLUNK_DB/wineventlog/thaweddb
