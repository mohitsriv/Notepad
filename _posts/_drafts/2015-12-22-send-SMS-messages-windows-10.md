---
layout: post
title: Send SMS messages and see missed calls with Windows 10 
description: "Send SMS messages and see missed calls with Windows 10"
modified: 2015-12-22
category: 
tags: [software, windows 10, mobile, phone]
imagefeature:
comments: true
share: true
excerpt_image: send-sms-send-sms-excerpt.png
---
I've been waiting a long time -- almost six+ years since the time Microsoft released Windows Phone to supersede [Windows Mobile](https://en.wikipedia.org/wiki/Windows_Mobile) -- for Microsoft to bring back the feature where you can type SMS messages on your desktop and send them through your phone. Thankfully, this feature, as well as the ability to get alerts when you miss a phone call, is [now here](http://www.winbeta.org/news/cortana-can-finally-send-sms-messages-offer-missed-call-alerts-in-latest-windows-10-desktop-build) with build 10565 for desktop. Both features are working great, but it took me one key step and a bit of reading to wrap my head around it.

# Feature overview #

After initially reading about the feature on various blogs, I wasn't sure if the send SMS feature requires Skype or any kind of additional download on the desktop. Turns out, you just need Windows 10 build 10565 of later. On your phone, you need Windows 10 [build 10572](https://blogs.windows.com/windowsexperience/2015/10/20/announcing-windows-10-mobile-insider-preview-build-10572/) or later.

On the desktop, the feature is integrated **really** seamlessly. It's just part of Cortana. You get started either by speaking (e.g. "Hey Cortana, send a text") or typing into the "Ask me anything" input box:

![Send SMS]({{ site.baseurl }}/images/send-SMS-send-SMS.png)

In addition, it's nice to be able to see missed phone calls. This is just part of the Windows 10 Action Center.

![See missed calls]({{ site.baseurl }}/images/send-SMS-action-center.png)

# Using your Exchange address book

While it's all really seamless, I did get hung up for awhile trying to get Cortana to know about my address book. And since the feature only lets you send messages to contacts in your address book, that made the feature unusable for me. Sharing how I solved this issue is the main motivation for the post.

My address book is in Exchange, and it turns out that Cortana can work both with address books in your Microsoft accounts (e.g. @hotmail.com or @outlook.com) as well as your work and school accounts (e.g. @&lt;myorganization&gt;.com). That is, **if** Windows 10 desktop knows about them. My Windows 10 home machine -- which is not domain joined -- did not know about my address book on Exchange.

You can resolve this by clicking on **Start &gt; Settings** and then searching for **Add or remove app, website, or email accounts**. Scroll down to the section for **Email, calendar and contacts** and choose **Add an account**. You'll see options for Outlook.com and Exchange. In fact, there are options for Google, Yahoo and iCloud as well, but I am not sure one way or the other if the address books are synced.

![Account settings]({{ site.baseurl }}/images/send-SMS-account-settings.png)

# Summary

After the initial bump in the road, the feature has been working great. I've been using it to keep pace with my iPhone friends who have the same feature on their Macs:) That said, I would love to see in newer builds the ability to also receive text messages on the desktop. Hope this helps! 