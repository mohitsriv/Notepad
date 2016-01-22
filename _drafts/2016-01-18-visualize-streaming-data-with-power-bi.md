---
layout: post
title: Visualize real-time data with Power BI and Azure Stream Analytics
description: "Visualize real-time data with Power BI and Azure Stream Analytics"
modified: 2016-01-21
category: 
tags: [hosted software, microsoft azure, IoT, Power BI, Stream Analytics]
imagefeature:
comments: true
share: true
excerpt_image: visualize-streaming-ex.png
---
I have been wanting to dabble with IoT (Internet of Things) for a little while now.  My interest, aside from the geek aspect, is uncovering some insight from analyzing signals over time. To get there, I am starting with the data collection and analysis side -- stitching together Microsoft Azure [Event Hubs](https://azure.microsoft.com/en-us/services/event-hubs/), [Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics/), and [Power BI](https://powerbi.microsoft.com/en-us/).

![Flow]({{ site.baseurl }}/images/visualize-streaming-ex-full.png)

Since wiring up an IoT device is part two, I looked for real-time signals available on the Internet that might be interesting to analyze.  Some ideas that came to mind:

- **Usage data for the the products I work on**: Which features are used the most? Which ones are experiencing the most growth? What is the usage by segment?
- **Logfiles for websites I operate**: Are there any request patterns that might indicate a DoS attack? Do users from different channels experience different usage patterns?
- **Sentiment on Twitter for a stock**: How does that relate to movements in the actual stock price?
- **My personal Twitter feed**: How many Tweets do I receive per time period? Which time of day and day of the week do I receive the most Tweets? Who are the most active Tweeters?

For this exercise, I went with the last option -- my personal Twitter feed. That said, armed with this toolchain, I look to improve the Power BI dashboards we already have available for the products I work on.

After doing a bit of research, I arrived at the following approach:

![Flow]({{ site.baseurl }}/images/visualize-streaming-flow.png)

1. **Articulate which questions** need to be answered, and **extract the necessary [*dimensions*](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)) and [*measures*](https://en.wikipedia.org/wiki/Measure_(data_warehouse))**: In the Twitter example, some key dimensions or independent/categorical variables are Hour, Day, DayOfWeek, Month and UserName. The key measures I care about along these dimensions are CountOfTweets and CountOfDistinctUsers. 
2. [**Optimize for real-time, or optimize for analytics**](http://stackoverflow.com/questions/30190297/how-to-join-excel-data-with-stream-analytics-data-in-power-bi): In its current form at least, Power BI gives a bit more flexibility as it relates to joins if you put the data in an intermediate store (e.g. Excel) first. But, since the ultimate goal is IoT analytics, I optimized for realtime and ensured the data stream I feed into Power BI is "denormalized" and contains all the dimensions I ultimately want to report on.
3. **Write an app that uses the Twitter streaming API to pump the data into Azure Event Hubs**: While perhaps overkill for my Twitter scenario, Event Hubs is ideal for the ultimate scenario -- receiving telemetry from potentially millions of devices. I used an [Azure App Service WebJob](https://azure.microsoft.com/en-us/documentation/articles/websites-webjobs-resources/) to host a console application that receives Tweets using the Twitter streaming API and sends them to Event Hubs.
4. **Process the real-time stream with Azure Stream Analytics**: Again while perhaps overkill for my Twitter scenario, Stream Analytics is ideal for taking a large stream of real-time data and grouping it by time.
5. **Visualize it with Power BI**

# Pump the data in Azure Event Hubs

# Process real-time streams with Azure Stream Analytics

# Visualize the data with Power BI
