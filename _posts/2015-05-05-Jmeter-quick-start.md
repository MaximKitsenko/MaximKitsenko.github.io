---
layout: post
title: JMeter introduction
---

During the software development process we often facing with need to test something. Depending on your goals and testing objects we choose the most appropriate testing tool.
Today i want to show  *Jmeter* and how it can be used. This article doesn't cover all Jmeter features and testing techniques. This is just brief description of Jmeter functionality, the goal of this article is to introduce JMeter.

**The problem.**
Sometimes queues in our the server are overloaded and users should wait a lot of time to see when their commands will be processed. Since the issue occurs only when the system is overloaded, after bug-fix will be published, we need to simulate overloading for testing purpose. Here occurs two questions:

1. How to emulate overloading easily ?	
2. How to check that user's commands will be processed in time?.

**Solution.**
There are a lot of tools capable to emulate high user activity and make functional testing. I want to consider one og the - Jmeter. 

**What is Jmeter?**
Jmeter is free and open source server performance testing tool. Written on Java (Swing Java API) it supports multithreading, it may be used to simulate a heavy load, analyse test results, make a graphical analysis of performance, test your server behavior under heavy concurrent load. Also Jmeter provides very easy and configurable way to do many kinds of testing. With help of Jmeter you can make some kinds of testing:

1. Load tests (system behavior under extreme conditions)
2. Performance tests (how the system responds and how is its stability for a specific workload)
3. Stress tests (break or overwhelm the system by loading more resources)
4. Functional tests (system functional requirements)

Basically, using JMeter consists of the creation, configuration, execution of test and the analysis of their results. 

**Simple test in Jmeter.**
For our goals we need some functional test that will work this way:  Send command to update -> Pull updated data -> Check that update was happened.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/2.png)

I have created two requests in Jmeter and configured their bodies, headers, etc. . The first one 'SyncSale' - should update sale, another one - 'GetSaleStatus' pulls data from the server.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/1.png)

We should add 3d element of the test - 'Assert', that should make sure that we received updated sale by 'GetSaleStatus' (also we can check response time, body content, response code and so on). Jmeter sends data very fast, obviously server can't process data in milliseconds and we should add delay between 1st and 2nd request. For this moment we have very simple test (send request -> make sure that response is 'ok' -> wait -> send another request-> make sure that response comes within specified time and contains specified data).

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/3.png)

**Test grows.**
One request can't overload our server and we should increase requests quantity. There are a lot of ways to do it (Jmeter is very flexible tool =) ). We can add requests in same manner as we did before or we can send requests in a loop. This ways requests will be started sequentially, also we can send requests in parallel by configuring Jmeter to use more then 1 thread.
We will add loop to send 'SyncSale' request many times. Each time we will change request body. For example, we can send first request with saleItemPrice = 1, next request can be saleItemPrice=2 and so on. Lets assume we want to send 10 commands, so the last request will have saleItemPrice = 10 in the request's body. When we call GetSaleStatus we should check that response contains salePrice==10, since it was the last command we sent before. (Obviously, you can change logic and add some variations for this test. For example instead of changing item's price for 1 sale you can change sale id in each request and set saleItemPrice to random number. Or you can create loop inside another loop. In this case you can update X sales Y times. You can check each sale from the loop by 'GetSaleStatus' or only check the last sale. The way you will choose depends from your imagination and goals.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/4.png)

At this moment we have test that sequentially updates sale, waits and checks that server response contains updated data. 

Jmeter can be easily configured to execute one test many times. To do it we jast need to specify threads count in configuration. Also there are a lot of other threading configurations. We can configurate warm-up time or loop threads many times or even endlessly threads loop. Since from Jmeter's point of view, each of the threads is a user - we can provide unique credentials, delays, request headers, etc. for each of the threads(we can specify these parameters in simple text files, or  hard code them, or generate randomly for all threads). 

**Test results**
Tests result can be visualized. For instance we can configure Jmeter to fail all responses except 200, or check body length or even body content or response time, here is exaple of how check result looks like:

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/7.png)

Graphs to view tests results (latency):

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/8.png)

Jmeter can be configured to send test results to some place in real-time. For example i configured graphite to show count of passed and failed tests:

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/9.png)



