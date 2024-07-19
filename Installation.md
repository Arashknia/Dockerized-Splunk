Our goal is: UF > IDX < Search Head (read and send logs by UF to IDX and use search-head to have search operations on them)
1. Download and install docker on your OS (I Will do it on my Windows 11 OS )
```https://hub.docker.com/```

2. go to services and make sure the docker service is running. the name of the service is varied but usually is "Docker Desktop Service". (it may be set on manual by default, change it to automatic start if you like). if you are on another OS like Linux or others, make sure the docker service is running

3. After installation look for the Splunk/Splunkk image from the docker desktop search box and then pull the image with the "latest" tag.
   > To date of this repository, the Splunk version is 9.2.2. I don't predict there will be a such change on Splunk which may cause issues with our configuration otherwise please inform and I will update the repository
on another OS you can do it with ```docker pull splunk/splunk```

5. after pull, wait for the pull process to complete in the docker-desktop notification tab.

6. download Splunk universal forwarder from Splunk. (you have to sign up to be able to download it with your Splunk account)
note: download it based on the OS to which you want to forward the log from it to Splunk.

7. install Splunk universal forwarder
   
-   7.1 On Windows use the customize option and click on the next.
-   7.2 Install it on the default directory and click on the next.
-   7.2 Don't fill in anything for an SSL certificate. just click on the next.
-   7.3 HERE IS THE MOST IMPORTANT PART. check the Local system (not the virtual account). because otherwise, the Splunk-forwarder service account won't have access to read all of our event-viewer channels or some system32 files. although if you miss this you can change it in the services tab of your Windows (with an administrator account) on the log-on tab of the universal-forwarder service setting.
-   7.4 Fill all the Check-boxes of all the event-viewer channels that you want to Splunk universal forwarder look into them and send their logs to the indexer. you may be ok to not check Enable AD monitoring if you are not a domain user
-   7.5 Set a username and password (not a random password)
-   7.6 Do not give an IP address for the Deployment server. because we don't need to, didn't, and won't implement one.
-   7.7 Give the IP address 127.0.0.1(localhost) (which is your PC) and the default port 9997. we are giving localhost because our indexer is in our host as a docker container which will be listening on port 9997 after implementation
-   7.7 Install and pray for me if you were too noob :D also, make sure the Splunk universal forwarder is running.

8. now get back to your docker-desktop. don't run the image with its default command mentioned in docker-hub. we need to make some changes to it to make sure all the necessary ports are opened and are binding between or host and the Splunk container. so run the command below: 
```
$ docker run -d -p 8000:8000 -p 8089:8089 -p 9997:9997 -e "SPLUNK_START_ARGS=--accept-license" -e "SPLUNK_PASSWORD=<password>" --name splunk splunk/splunk:latest
```
> Hint: Splunk default ports that need to be opened between your host and Splunk container are:
> - 9997 for forwarders to the Splunk indexer.
> - 8000 for clients to the Splunk Search page
> - 8089 for splunkd (also used by the deployment server which we don't need for our current implementation).
