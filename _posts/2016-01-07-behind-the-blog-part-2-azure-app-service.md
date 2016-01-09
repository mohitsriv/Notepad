---
layout: post
title: Behind the Blog Part 2 - Microsoft Azure App Service
description: "Behind the Blog Part 2 - Microsoft Azure App Service"
modified: 2016-01-07
category: 
tags: [hosted software, microsoft azure]
imagefeature:
comments: true
share: true
excerpt_separator: <!--more-->
excerpt_image: behind-the-blog-part-2.png
---
In Part 1 of this series, I summarized the tech behind this blog.  This post will dive deeper into the [Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) piece -- how to Git deploy a [Jekyll](https://jekyllrb.com/)-generated static site and how to use the URL rewrite module to ensure friendly URLs mindful of SEO.<!--more-->

![Behind the blog - App Service]({{ site.baseurl }}/images/behind-the-blog-part-2.png)

If you are generating your site locally with Jekyll or another static file generator, then App Service is the only cloud piece in the above diagram you need. You simply Git deploy or FTP your generated files to App Service.  However, using GitHub and Travis gives you authoring, source control and CI in the cloud (all requirements for me) and will be detailed in future posts.

# Friendly URLs

I had three requirements around URLs:

1. **Requests to non-primary domains redirect to a single primary domain**: By virtue of being on App Service, my site is automatically exposed via [cloudmouth.azurewebsites.net](http://cloudmouth.azurewebsites.net).  And, due to links to my old blog, I also wanted to support my old primary domain, [www.cloudmouth.net](http://www.cloudmouth.net).  That said, I wanted both of them to [301 redirect](https://en.wikipedia.org/wiki/HTTP_301) to [cloudmouth.net](http://cloudmouth.net) so search engines can find my content on exactly one page and assign page rank accordingly instead of spreading it across multiple pages.
2. **Requests to my old blog redirect to the corresponding page on my new blog**: My old blog's primary domain was different (www.cloudmouth.net vs. cloudmouth.net). And, posts had a slightly different URL structure (with the date in the URL). I wanted links to my old blog to still work for usability and SEO reasons. 
3. **URLs do not contain an .html extension**: Jekyll generates files with .html extensions. But, when rendering links, it assumes no .html extension. So, I needed App Service to serve those pages without requiring the .html extension.

I could meet all three requirements with **URL rewrite rules** in the **web.config** file! This is because App Service runs the same URL rewrite module that has been part of IIS for many years. *The equivalent on other Web servers is the **.htaccess** file.* My rules are below, along with some explanation. Alternatively, you can see them on [GitHub](https://github.com/mohitsriv/Blog/blob/master/web.config).

## Single primary domain

This rule says to 301 redirect any requests with hosts other than http://cloudmouth.net (e.g. http://cloudmouth.azurewebsites.net) to the same URL but with the host part changed to http://cloudmouth.net.  

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
<system.webServer>
<rewrite>
<rules>
  <rule name="Single Primary Domain" stopProcessing="true">
    <match url="(.*)" ignoreCase="false" />
    <conditions logicalGrouping="MatchAll">
      <add input="{HTTP_HOST}" pattern="" ignoreCase="false" />
      <add input="{HTTP_HOST}" negate="true" pattern="^cloudmouth\.net" ignoreCase="false" />
    </conditions>
    <action type="Redirect" url="http://cloudmouth.net/{R:1}" redirectType="Permanent" />
  </rule>    
</rules>
</rewrite>
</system.webServer>
</configuration>
{% endhighlight %}

## Redirect from posts on old blog to posts on new blog

These 301 redirect rules, unlike the previous one, match the part of the URL following the host. It's a family of rules that map pages & posts on my old blog to pages & posts on my new blog. The first rule removes the date part of the URL. The other rules account for differences in dashes vs. spaces and re-branding.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
<system.webServer>
<rewrite>
<rules>
  <rule name="oldblogtonewblog" stopProcessing="true">
    <match url="200[0-9]/[0-9][0-9]/[0-9][0-9]/(.*)/" />
    <action type="Redirect" url="{R:1}" redirectType="Permanent" />
  </rule>    
  <rule name="oldblogtonewblog3" stopProcessing="true">
    <match url="category/windows-azure/" />
    <action type="Redirect" url="tags/#microsoft%20azure" redirectType="Permanent" />
  </rule>
  <rule name="oldblogtonewblog4" stopProcessing="true">
    <match url="category/(.*)\-(.*)/" />
    <action type="Redirect" url="tags/#{R:1}%20{R:2}" redirectType="Permanent" />
  </rule>    
  <rule name="oldblogtonewblog5" stopProcessing="true">
    <match url="category/(.*)/" />
    <action type="Redirect" url="tags/#{R:1}" redirectType="Permanent" />
  </rule>
</rules>
</rewrite>
</system.webServer>
</configuration>
{% endhighlight %}

## No .html extension

There are actually two types of rules in play here:

1. 301 redirect any request with a .html extension to one without the .html extension
2. Have App Service append the .html extension *under the covers* (but not visible to the user) so that the extensionless URLs get mapped to an actual .html file in the filesystem.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
<system.webServer>
<rewrite>
<rules>
  <rule name="extensionless" stopProcessing="true">
    <match url="(.*)\.html$" />
    <action type="Redirect" url="{R:1}" redirectType="Permanent" />
  </rule>
  <rule name="removeextension" enabled="true">
    <match url=".*" negate="false" />
    <conditions>
      <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
      <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
      <add input="{URL}" pattern="(.*)\.(.*)" negate="true" />
    </conditions>
    <action type="Rewrite" url="{R:0}.html" />
  </rule>
</rules>
</rewrite>
</system.webServer>
</configuration>
{% endhighlight %}

# Git deploy

One reason I chose App Service is for the ability to use Git for deployment. Most CI solutions have great support for Git. And, on the App Service side, using source control for deployment gives history along with the ability to roll back.

![Behind the blog - App Service]({{ site.baseurl }}/images/behind-the-blog-part-2-CI.png)

Uploading the generated site is just standard Git.

1. Clone the active site locally or in your CI system.
2. Copy the newly generated site over the cloned site.
3. Add the changed files, and commit.

{% highlight bash %}
# clone the latest version of the blog from App Service
git clone https://$AZURE_WA_USERNAME:$AZURE_WA_PASSWORD@cloudmouth.scm.azurewebsites.net:443/cloudmouth.git ../Blog.generated

# copy generated HTML site to 'generated' branch
cp -R _site/* ../Blog.generated
cd ../Blog.generated

# commit and push
git add -A .
git commit -a -m "Build #$BUILD_NUMBER"
git push origin master
{% endhighlight %}

# Next

Hope this helps.  Anything you would do differently?  Let me know in the comments.