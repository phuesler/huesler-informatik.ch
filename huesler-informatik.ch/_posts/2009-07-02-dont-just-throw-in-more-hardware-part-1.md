---
layout: post
title: Don't just throw in more hardware (Part 1)
---

<p>In this post, I take a stand against the strategy to solve all performance issues of a web application by throwing in more and bigger hardware. While it is in fact true, that hardware is cheap in comparison, you will inevitably hit a brick wall some time in the near future, if your traffic keeps growing and you don't change your application to perform better. Of course, you might need bigger and better hardware at some point, but with a few relatively simple steps, you can postpone that investment. This is part 1 of a series of blog posts.</p>

<p>So you have deployed your brand new web app. There where a few minor issues during the deployment but nothing to worry about. One day you realize that your web app becomes slower each day as the user base grows and the traffic increases. What can you do to improve this?</p>

<p>
Well, you could call in the big guns and install more and bigger machines, hardware load balancers, caching solutions, reverse proxies and database replication. If that is not enough, there is always database sharding. This will cost a lot of time, money and expertise to get this setup up and running. Of course, these resources are very limited and you would rather spend them on new features. If you don't, your company may no longer exist in a few months.
</p>

<p>
Here is the good news: You can start off by taking smaller steps. I try to follow the principles <a href="http://en.wikipedia.org/wiki/You_Ain't_Gonna_Need_It">YAGNI</a> and <a href="http://en.wikipedia.org/wiki/KISS_principle">KISS</a> when developing software, so why not follow them when improving the performance of a website, right? So let's get started.
</p>

<p>
What means faster? In general, it is referred to something like requests per second combined with the number of concurrent users. But that is not the whole story. In my opinion, the performance of a website should be measured in the page responsiveness perceived by the user. So that includes the time needed to display a page.
</p>


<p>Assuming a rather standard web application, we are going to take the following steps to improve the performance of our web application.
  <ul>
    <li>Client side optimizations and caching</li>
    <li>Hotspot identification</li>
    <li>Performance benchmark</li>
    <li>Simple code and architecture optimizations</li>
    <li>Page caching</li>
    <li>Fragment caching</li>
  </ul>
</p>
<p>
In the next part I will talk about page responsiveness and client side caching, so stay tuned.</p>
