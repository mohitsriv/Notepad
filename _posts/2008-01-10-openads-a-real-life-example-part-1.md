---
layout: post
title: Openads – A Real Life Example, Part 1
description: "Openads concepts."
modified: 2008-01-10
category: 
tags: [hosted software]
imagefeature:
comments: true
share: true
---
I recently setup [Openads](http://www.openads.org/) for Faves.com. Openads is a powerful ad server that lets your manage how ads are delivered on your site. It is similar to Google’s Doubleclick except that a) Openads is free and b) you can host it yourself.

You begin by telling Openads about your inventory. This is done by defining zones and then assigning the appropriate advertising campaigns to these zones. You then customize delivery (i.e. with what frequency or under what conditions a specific campaign for a zone is shown) by defining delivery rules.

While the documentation is reasonably good, I had trouble getting my head around the terminology without a real-life example. So, I am including our configuration, slightly simplified and modified, over my next few posts. Enjoy!

For this post, here is how Openads [terminology](http://docs.openads.org/openads-2.4-guide/adserving-concepts.html) maps to our scenario:

- Advertiser: Google
  - Campaign: AdSense
    - Banner: 468×60
    - Banner: 728×90
- Advertiser: Amazon
  - Campaign: Omakase
    - Banner: 468×60
    - Banner: 728×90
- Advertiser: AcmeMediaGroup
  - Campaign: FunnyWigs.Com (Direct Ad Sale)
    - Banner: 468×60
    - Banner: 728×90
- Publisher: Faves.com
  - Zone: 468×60 – Top
  - Zone: 728×90 – Top
  - Channel: shopping - Allows the publisher to request ads specifically targeted to shopping inventory.
  - Channel: nonshopping

In my next post, I will talk about delivery rules. These tell Openads when to show each of your banners.