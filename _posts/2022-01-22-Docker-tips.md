# Deploy ELK locally

How to deploy ELK as docker containers on DockerDescktop (windows)

Here is config file used.

But after run received this error

```Starting es01 ... done
Attaching to es01
es01 exited with code 137
```

This is because it's not enougth RAM. To extend ram limits

open ```C:\Users\%USERPROFILE%\.wslconfig```
line 
```memory=3GB```
changed to  
```memory=4GB``` 


useful links: 
https://www.elastic.co/guide/en/kibana/current/docker.html

after configuring kibana faced with issue:
```
Unable to retrieve version information from Elasticsearch nodes. connect ECONNREFUSED 
```
