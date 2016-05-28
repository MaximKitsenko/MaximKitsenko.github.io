---
layout: post
title: JMeter quick start
---

During the software development process we often facing with need to test something. Depending on your goals and testing objects we choose the most appropriate testing tool.
Today i want to show  *Jmeter* and how it can be used. This article doesn't cover all Jmeter features and testing techniques. It is just extract of useful concepts and approaches. Lets start.

**The problem**.
Sometimes queues on the server are overloaded and users should wait a lot of time to see when their commands will be processed. Since the issue occurs only when the system is overloaded, after bug-fix will be published, we need to simulate overloading for testing purpose. The question is 'How easily emulate overloading ?'. The second question is 'How to check that user's commands will be processed in time?'.

**Solution**
There are a lot of ways to emulate high user activity to overload the system. Since system provides ability to import data we can import large files, use curl to send requests, use API which provides an easy way to communicate with the system or something else. JMeter also can be used to for these goals. We will use it to send thousands of API requests. You can ask why to use Jmeter ? The quick answer is: Jmeter is very powerful tool that provides very easy and configurable way to do it. 

In short it works this way:
I have created two requests with headers and body contains user tokens. The first one 'SyncSale' - should update sale, another one - 'GetSaleStatus' pulls data from the server. After we had received response from the 2nd request, we should check that we receive correct data (also we can check response time, body content and so on). 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/1.png)
