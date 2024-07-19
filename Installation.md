1. Download and install docker on your OS (I Will do it on my Windows 11 OS )
```https://hub.docker.com/```

2. go to services and make sure the docker service is running. the name of the service is varied but usually is "Docker Desktop Service". (it may be set on manual by default, change it to automatic start if you like). if you are on another OS like Linux or others, make sure the docker service is running

3. After installation look for the Splunk/Splunkk image from the docker desktop search box and then pull the image with the "latest" tag. To date of this repository, the Splunk version is 9.2.2. I don't predict there will be a such change on Splunk which may cause issues with our configuration otherwise please inform and I will update the repository
on other OS you can do it with ```docker pull splunk/splunk```

5. after pull, wait for the pull process to get complete in the docker-desktop notification tab, or do part 5 below.

6. download Splunk universal forwarder from Splunk. (you have to sign up and download it with your Splunk account)
note: download it based on the OS to which you want to forward the log from it to splunk.
---
Hint: Splunk default ports that need to be opened between your host and containers are:

- 9997 for forwarders to the Splunk indexer.
- 8000 for clients to the Splunk Search page
- 8089 for splunkd (also used by the deployment server which we don't need for our current implementation).

