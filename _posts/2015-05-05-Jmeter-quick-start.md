---
layout: post
title: JMeter introduction
---

During the software development process we often facing with need to test something. Depending on your goals and testing objects we choose the most appropriate testing tool.
Today i want to show  *Jmeter* and how it can be used. This article doesn't cover all Jmeter features and testing techniques. This is just brief description of Jmeter functionality, the goal of this article is to introduce JMeter.

**The problem**.
Sometimes queues in our the server are overloaded and users should wait a lot of time to see when their commands will be processed. Since the issue occurs only when the system is overloaded, after bug-fix will be published, we need to simulate overloading for testing purpose. Here occurs two questions:

1. How to emulate overloading easily ?	
2. How to check that user's commands will be processed in time?.

**Solution**
There are a lot of tools capable to emulate high user activity and make functional testing. I want to consider Jmeter In this article. Jmeter is server performance testing tool, supports multithreading, it may be used to simulate a heavy load, analyse test results, make a graphical analysis of performance, test your server behavior under heavy concurrent load. Also Jmeter provides very easy and configurable way to do many kinds of testing. 

**Simple test in Jmeter**
For our goals we need some functional test that will work this way:  

1. Send command to update;	
2. Pull updated data;	
3. Check that update was happened.


![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/2.png)

I have created two requests in Jmeter and configured their bodies, headers, etc. . The first one 'SyncSale' - should update sale, another one - 'GetSaleStatus' pulls data from the server, the 3d element of the test - 'Assert', we should make sure that we received updated sale by 'GetSaleStatus' (also we can check response time, body content, response code and so on). 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/1.png)

Jmeter sends data very fast, obviously server can't process data in milliseconds and we should add delay between 1st and 2nd request. For this moment we have very simple test (send request -> make sure that response comes within specified time and contains specified data -> wait -> send another request-> make sure that response comes within specified time and contains specified data). We can configure Jmeter to run this test forever and send check result to some place, for example  graphite. 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/3.png)

**Test grows**
But 1 request can't overload our server and we should increase requests quantity. There are a lot of ways to do it (Jmeter is very flexible tool =) ). We can add requests in same manner as we did before. This way requests will be started sequentially, we can send requests in a loop, also we can start sending requests in parallel or combine all these methods (like we will do below).
We will add loop to send 'SyncSale' request many times. Each time we will change request body. For example, we can send first request with saleItemPrice = 1, next request can be saleItemPrice=2 and so on. Lets assume we want to send 10 commands, so the last request will have saleItemPrice = 10 in the request's body. When we call GetSaleStatus we should check that response contains salePrice==10, since it was the last command we sent before. (Obviously, uou can change logic and add some variations for this test. For example instead of changing item's price for 1 sale you can change sale id in each request and set saleItemPrice to random number. Or you can create loop inside another loop. In this case you can update X sales Y times. You can check each sale from the loop by 'GetSaleStatus' or only check the last sale. The way you will choose depends from your imagination and goals.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/4.png)

Tests result can be visualized. We can configurate our checks. For instance we can configure Jmeter to fail all responses except 200, or check body length or even body content or response time.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/7.png)

At this moment we have test that sequentially updates sale (or many sales), waits and checks that server sends responses with correct data. As you can notice there is no parallelization and this test still can't overload server with requests that we send sequentially.
Fortunately we can configurate Jmeter to execute one test many times in parallel within specified time range or send these requests in many threads many times or even send them endlessly.

As we did with loops we can configure our requests to be unique for one thread or be the same for all threads. Since from Jmeter point of view, each of the threads is a user - we can provide unique credentials, delays, request headers, etc. for each of the threads(we can psecify these parameters in simple text files, or leave hard coded, or generate randomly for all threads).

**Test results**
We can visualize out tests. Jmeter provides few graphs to view tests results.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/8.png)

Also i configured Graphite to show our tests in real-time 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/9.png)



