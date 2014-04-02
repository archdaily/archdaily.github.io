---
layout:     post
title:      "How to get a daily HTML report email from Google Analytics"
author:     Gustavo Garcia G
date:       2014-04-02 15:00:00
categories: analytics
tags:       reports drive analytics
---

{% img center /images/dailyreport.png 619px 393 'title text' 'alt text' %}

I love getting daily reports from all our services. It's very useful to see a snapshot of what's going on with a service. 

One day I decided I wanted a daily report from Google Analytics, so I started searching how to get it. First, I set up a Dashboard, then I realized that it could be possible to get this dashboard on my email every day... sweet! Then I got my automatic email and what did I get? An ugly PDF file attached to that email. I was really expecting a little more coming Google.

Then, browsing the web, I found this tutorial: ["Building Dashboards Using Google Analytics and Google Apps Script"][dashboards]

<iframe style="text-align:center;margin:auto;display:block;" width="420" height="315" src="//www.youtube.com/embed/rL4N3qFyycg" frameborder="0" allowfullscreen></iframe>

It actually inspired me to make a custom email report, using *Google Scripts*.

The Idea
========

- Use the script called **"Google Analytics Report Automation (Magic)"** in order to get data into our Google SpreadSheet
- Write another script, called **Mailer** that reads the data from the Google SpreadSheet and builds an email
- Configure a trigger based on time (every day)

It's pretty simple, actually

The Solution
============

The solution is very specific to our site, but the idea may be applicable to all sites. I think you'll probably want the same initial part (total pageviews, visitors, average time on site, and bounce rate).

Let's asume we have the *Google Analytics Report Automation (Magic)* script running, and the first *core report* looks like this:

- query1:      **value1**
- type:        **core**
- ids:         **ga:your_id** 
- last-n-days: **8**
- metrics:     **ga:visits,ga:pageviews,ga:avgTimeOnSite,ga:visitBounceRate**
- dimensions:  **ga:date**
- sheet-name:  **total**

Basicaly, we are fetching *visits*, *pageviews*, *average time on site* and *visit bounce rate* for the last *8 days*. And we want to get this data on a sheet called "total".

We can run this script (calling the *Get Data* function) and see what will we get on the sheet *total*. Nice!

Now, the **mailer script**:

{% gist 9939088 AnalyticsMailer.gs %}

As you can see, I used a HTML Template, that can be found here:

{% gist 9939088 emailTemplate.html %}

In these gists I included a **most visited articles** variable, in order to show how you could do it.



[dashboards]: http://analytics.blogspot.com/2012/08/automate-google-analytics-reporting.html
