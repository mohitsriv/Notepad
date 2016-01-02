---
layout: post
title: Behind the Blog Part 1 - Tech Overview 
description: "Behind the blog part 1 - tech overview"
modified: 2015-12-31
category: 
tags: [hosted software, jekyll, microsoft azure]
imagefeature:
comments: true
share: true
excerpt_separator: <!--more-->
excerpt_image: behind-the-blog-flow.png
---
As part of taking the blog out of hibernation, I updated the technology that powers this blog. I've migrated the blog engine from WordPress + MySQL to [Jekyll](https://jekyllrb.com/) hosted on [Microsoft Azure App Service](https://azure.microsoft.com/en-us/services/app-service/). I am satisfied with the resulting workflow and share the details below. While it looks like a lot of steps, day-to-day authoring is really simple -- just checking in Markdown files to GitHub.<!--more-->

![Behind the blog - workflow]({{ site.baseurl }}/images/behind-the-blog-flow.png)

# Requirements #

First off, for learning reasons, I wanted to use some technologies I am less familiar with -- especially on the framework and CI side. That meant no ASP.NET and no VS Online. Beyond that, I had the following goals and requirements.  "I can":

- **Authoring**
  - Author posts in Markdown
  - Run a local copy of my blog for authoring and testing in disconnected settings
  - Alternatively, make updates entirely online, especially from tablet and phone
  - Migrate posts from my existing WordPress blog
  - Use a good starting template, so I don't have to start completely from scratch
- **Hosting**
  - Redirect visitors of pages/posts on my old WordPress blog to the appropriate pages/posts on my new blog, properly using 301 redirects
  - Offer SSL with a custom domain (e.g. https://cloudmouth.net), although I haven't gotten to this one just yet
  - Run without a database
  - Get high performance
  - Perform little or no ongoing maintenance (e.g. upgrades, security patches, etc.)
- **Blog features**
  - Have site pages in addition to posts
  - Offer comments

# Jekyll, Markdown and GitHub

The above requirements quickly led me to [GitHub Pages](https://pages.github.com/). It is static file hosting but with a twist. GitHub Pages [supports Jekyll](https://help.github.com/articles/using-jekyll-with-pages/), a simple blog-aware static site generator. I was a bit apprehensive about using a static site generator instead of dynamically generating pages with a server side framework. But, Jekyll supports *includes* and *templating* giving it some of the power of a server side framework. This makes it possible to generate at least somewhat sophisticated and personalized sites while not having to copy stuff (e.g. headers, footers, index pages) all over the place. It's likely sufficient for many blogs but wouldn't quite work for sites with requirements beyond that -- e.g. implementing authenticated access, rendering pages server side based on a database or user agent, and generating urls/links based on the host header. And, where I did need interactive experiences I had to "outsource" -- e.g. Disqus for the comments. On the flip side, I have the piece of mind that my site is entirely static, effectively eliminating the need to think about security patches.

I also found a template I liked: [hmfaysal/Notepad](https://github.com/hmfaysal/Notepad). It implements responsive design, has a distinctive look, and integrates both Disqus and Google Analytics. I could customize it simply by [forking it and making changes](https://github.com/mohitsriv/Blog)!

That said, I realized GitHub Pages did not meet all my requirements. I was unable to:

- Use 301 redirects: While GitHub Pages supports [JekyllRedirectFrom](https://github.com/jekyll/jekyll-redirect-from), this plugin uses the HTTP-REFRESH meta tag instead of the server returning 301. This can degrade the user experience slightly and [potentially](http://stackoverflow.com/questions/5392001/seo-consequences-of-redirecting-with-meta-refresh) impact how search engines treat your site.
- Offer SSL with a custom domain (e.g. https://cloudmouth.net)

Also, I wanted a bit more control and insight over my hosting in the future. Some examples include choosing the geographic region and doing staged deployments. In addition, I wanted a bit more in the way of CI -- being able to run an HTML validator, for example, before actually publishing a blog post.

# Microsoft Azure App Service

So, I brainstormed a bit about a model where I could still use GitHub and Jekyll but host elsewhere. I ultimately landed on using [Microsoft Azure App Service](https://azure.microsoft.com/en-us/services/app-service/) for hosting and [Travis CI](https://travis-ci.org/) for automatically testing, building and publishing to Azure from GitHub. In addition, I use Disqus for comments. From a day-to-day authoring perspective, it is as simple as GitHub Pages. But, I can host on Azure and run additional automation before publishing.

I chose Azure App Service in part because I work on it and am super familiar with it. That aside though, it is an awesome PaaS offering. It's both really easy to use and full of features. While it is fashionable to host static sites in Azure Storage or AWS S3, I went with App Service because I wanted Git deployment + history/rollback, SSL with a custom domain and support for 301 redirects as described earlier.

# Travis CI

Finally, I needed a place to run the Jekyll build process for generating my site. When I got started with Jekyll, I just built locally and used FTP to upload to App Service. While this worked okay, it had limitations.  The biggest was that I need to be at a desktop or laptop to author. I couldn't author from a device that cannot run git -- such as my phone. So, I sought out a CI solution. There are lots out there, and I can't say I was completely thorough in the process. But, I chose Travis for these reasons:

- It's a hosted service. I don't need to run anything.
- It's free for public repositories on GitHub, such as [that of my blog](https://github.com/mohitsriv/blog).
- It integrates with GitHub.
- It has good support for Ruby. While I am not specifically versed or attracted to Ruby, it is needed to build Jekyll. 
- By virtue of using Docker containers, it gives me a completely clean build environment every time.

# Next

Overall, the resulting workflow is as simple as using GitHub Pages but with more CI and more hosting features. That said, I do miss a bit being able to dynamically generate pages with a server side framework like ASP.NET or Node.js.

In future posts, I'll detail each of the parts -- Jekyll, GitHub, Travis CI and Microsoft Azure App Service.