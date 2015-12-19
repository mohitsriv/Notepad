---
layout: post
title: Same Blog (Mostly), a New Name, and SEO
description: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
modified: 2009-10-24
category: hosted software
tags: []
imagefeature:
comments: true
share: true
---
As you may have noticed, I have rebranded this blog from This Hosted Life to Cloud Mouth. A few weekends ago, I decided I would like to start blogging again and with a greater emphasis on Windows Azure, the cloud services platform I am working on at Microsoft. So, I went to GoDaddy’s domain name search and decided not to get up until I found a domain name I liked. I sort of knew I wanted “cloud” in the name, and it eventually hit me that I could play off the fact that “cloud” rhymes with “loud”. Unfortunately, cloudmouth.com was unavailable. But, I was okay with a .net domain name instead, and thankfully cloudmouth.net was available!

![Rebrand]({{ site.url }}/images/same-blog-rebrand.png)

# SEO Implications
Having been through a rebranding before, I knew one of the most important next steps was to make sure every inbound link to the old site is 301 redirected to the new site. With this type of redirect, search engines (in theory at least) know to automatically transfer page rank from the old url (e.g. http://thishostedlife.com/about) to the new url (e.g. http://www.cloudmouth.net/about). This is not always the case with other forms of redirection.

You can do this with IIS or Windows Azure with the following set of rewrite rules that tell the Web server to 301 redirect all requests for anything other than http://www.cloudmouth.net/… to http://www.cloudmouth.net/….

{% highlight xml %}
<rewrite>
  <rules>
    <rule name="Imported Rule 1" stopProcessing="true">
      <match url="(.*)" ignoreCase="false" />
      <conditions logicalGrouping="MatchAll">
        <add input="{HTTP_HOST}" pattern="" ignoreCase="false" />
        <add input="{HTTP_HOST}" negate="true" pattern="^www\.cloudmouth\.net" ignoreCase="false" />
      </conditions>
      <action type="Redirect" url="http://www.cloudmouth.net/{R:1}" redirectType="Permanent" />
    </rule>
  </rules>
</rewrite>
{% endhighlight %}

If you aleady have an equivalent set of rules for Apache, such as the ones below, these can be easily converted by Microsoft’s IIS Manager for consumption by IIS or Windows Azure.

{% highlight apache %}
<IfModule mod_rewrite.c>
Options +FollowSymLinks
RewriteEngine on
RewriteCond %{HTTP_HOST} .
RewriteCond %{HTTP_HOST} !^www\.cloudmouth\.net
RewriteRule (.*) http://www.cloudmouth.net/$1 [R=301,L]
</IfModule>
{% endhighlight %}

Finally, you can use a tool such as Web-Sniffer, one of my favorites, to confirm all your old pages return HTTP Status Code: 301 Moved Permanently and that your hard-earned page rank will be (hopefully!) retained.