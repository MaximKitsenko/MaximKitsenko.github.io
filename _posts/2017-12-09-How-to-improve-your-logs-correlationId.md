---
layout: post
title: How to improve your logging with CorrelationId
---

Important part of any application is logging. Readable log,  which can be easily searched, significantly helps to solve problems in the system and can save a lot of time and money.

Few weeks ago we decided to improve our logging system for our integration microservice. Integration is part of the system which makes a lot of API calls for 3d-party systems. And one initiating request can end up generating multiple more downstream service calls.

![_config.yml]({{ site.baseurl }}/images/2017-12-09-How-to-improve-your-logs-correlationId/1.png)

Sometimes these API calls may fail, in such cases it may become difficult to diagnose what happened. Often we need to see the error in wider context of initiated call. In other words it is useful to trace the calls chain, navigating upstream or downstream, like we do with stacktrace.

To acheive it we can link log records in a such way that will allow us to see what calls what. We decided to use a marker which is GUID. When first call made - we generate this GUID. After that we put this GUID to further calls. Each new call may append this `marker`. Variations are possible, you can choose more possible layouts for this `marker`, generate new `marker` on each call and append it to parent `marker` etc.

![_config.yml]({{ site.baseurl }}/images/2017-12-09-How-to-improve-your-logs-correlationId/3f3768a2-c899-472d-9778-59dd354f3aeb.png)

Aggregating such logs allows us to trace our call all the way throught the system.

![_config.yml]({{ site.baseurl }}/images/2017-12-09-How-to-improve-your-logs-correlationId/2.png)

Now we:

- can track all sort of interactions;
- can identify costly transactions;
- have more detailed picture of how things are going.