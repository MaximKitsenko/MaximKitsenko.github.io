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

Jmeter is server performance testing tool, supports multithreading, it may be used to simulate a heavy load, analyse test results, make a graphical analysis of performance, test your server behavior under heavy concurrent load. For aour goals we need some script that will work this way:  sendscommand to update, after that pull updated data to check that update was happened.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/2.png)

I have created two requests with headers and body contains user tokens. The first one 'SyncSale' - should update sale, another one - 'GetSaleStatus' pulls data from the server. After we had received response from the 2nd request, we should check that we receive correct data (also we can check response time, body content and so on). 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/1.png)

Jmeter sends data very fast, essentially server can't process data in milliseconds and we should add delay between 1st and 2nd request. For this moment we have very simple test (send request -> make sure that response comes within specified time and contains specified data -> wait -> send another request-> make sure that response comes within specified time and contains specified data). We can configure Jmeter to run this test forever and send check result to some place (i had been configuring graphite to show info from the test). 

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/3.png)

But 1 request can't overload our server and we should increase requests quantity. There are a lot of ways to do it (Jmeter is very flexible tool =) ). We can add requests in same manner as we did before. This way requests will be started sequentially, we can send requests in a loop, also we can start sending requests in parallel or combine all these methods.
We will add loop to send 'SyncSale' request many times. Each time we will change request body. For example we can send first request with saleItemPrice = 1, next request can be saleItemPrice=2 and so on. Lets assume we want to send 10 commands, so the last request will have saleItemPrice = 10 in the request's body. When we call GetSaleStatus we should check that response contains salePrice==10. You can change logic and add some variations for this test. For example instead of changing item's price for 1 sale you can change sale id in each request and set saleItemPrice to random number. Or you can create loop inside another loop. In this case you can update X sales Y times. You can check each sale from the loop by 'GetSaleStatus' or only check the last sale. The way you will choose depends from your imagination and goals.

![_config.yml]({{ site.baseurl }}/images/2015-05-27-Jmeter-quick-start/4.png)



