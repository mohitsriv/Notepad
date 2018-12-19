---
layout: post
title: Deploying and hosting Jekyll sites using the AWS Amplify Console
description: ""
modified: 2018-12-19
category: 
tags: [hosted software, jekyll, aws, aws amplify]
imagefeature:
comments: true
share: true
excerpt_image: jekyll-amplify.png
---
Three years ago, I updated the technology that powers this blog, migrating from WordPress + MySQL to [Jekyll](https://jekyllrb.com), a simple, extensible static site generator. Recently, I made an additional change and started deploying and hosting this blog using a product I have been working on at AWS, the recently announced [AWS Amplify Console](https://console.amplify.aws). Together, Jekyll + the Amplify Console provide a complete and agile workflow for authoring, testing, deploying, and hosting my blog. In this post, I share some of the tips and tricks I discovered along the way.

![Jekyll and Amplify]({{ site.baseurl }}/images/jekyll-amplify.png)

# Why a static site generator? #
The primary reason I chose a static site generator like Jekyll was simplicity. The input is simply text files that can be versioned easily in source control, and the output is simply static files like HTML, CSS, and JavaScript. There is no custom content management UI to learn, no database to manage, and no blog serving software to keep up-to-date with the latest security patches. Because the output is static files, this means any web server (such as Amazon CloudFront and Amazon S3) can serve the output of a static site generator without requiring extensions like language runtimes. This brings even more benefits--speed, security, and scalability--over dynamic blog engines or content management systems.

# Why the AWS Amplify Console? #
As great as static websites are, you may need additional hosting features, or you may need a deployment workflow that allows you to easily work on and test new versions of your site either by yourself or with a team. And, you want all of this to be easy. Enter the Amplify Console. For hosting, it offers features like distinct staging sites/environments for each branch in Git, password protection for these staging sites, url redirects/rewrites, easy custom domain setup, and automatic friendly subdomains for each non-production site. For deployments, it automates everything when you checkin your source. A build is triggered in the cloud, followed by screenshot generation, and finally deployment. If you are building a static web app (e.g., using frameworks such as React, Vue.js, Ionic, or Angular) that requires backend functionality, Amplify Console offers additional features. But, that is for another post.

To get started, simply click through a few screens in the [Amplify Console](https://console.amplify.aws). First, select your GitHub repo.

![Jekyll and Amplify]({{ site.baseurl }}/images/jekyll-amplify-step-1.png)

Amplify Console detects that this particular repo is the source for a Jekyll site. And, it automatically figures out the build settings.

![Jekyll and Amplify]({{ site.baseurl }}/images/jekyll-amplify-step-3.png)

To deploy the site, simply complete the wizard. Within minutes, the site will be available at https://&lt;branch&gt;.&lt;appid&gt;.amplifyapp.com/. For more on getting started with the Amplify Console, take a look at the [docs](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html). 

# Tips and tricks #
In the rest of this post, I share some of the less obvious tips and trips I discovered for marrying Jekyll with the Amplify Console. 

## Tip 1 - Jekyll configuration with multiple environments ##
One of my requirements is to be able to publish my blog to a staging site so I can see how it will look and perform before I push to production. While I can and do build my Jekyll site locally as well, publishing it to a staging site allows me to test across a variety of devices and also to make it available to others. Git + the Amplify Console offer a natural solution for this. In Git, I simply create a staging branch where I make my changes. When I then push these changes to GitHub, AWS Amplify picks up these changes, performs the Jekyll build, and makes the site available at https://staging.cloudmouth.net side-by-side with https://cloudmouth.net. Cool!

The problem is that my Jekyll template generates absolute urls for certain scenarios such as the RSS feed where relative urls won't work. So, Jekyll needs to know at build time what the base url is. In my case, that is either https://staging.cloudmouth.net or https://cloudmouth.net depending on whether it is staging or production. Jekyll provides a solution for this by accepting [multiple configuration files](https://jekyllrb.com/docs/configuration/options/#build-command-options) as input to the build. Settings in later files override settings in earlier files. For example:

{% highlight bash %}
bundle exec jekyll b --config _config.yml,_config.env.yml
{% endhighlight %} 

The **_config.env.yml** file is environment specific. In this case, it contains the environment-specific base url:

{% highlight yaml %}
# Change url to your domain. Leave localhost server or blank when working locally.
# or use the hint from http://jekyllrb.com/docs/github-pages/ to keep this the same 
# whether working locally or remotely. Start Jekyll with: jekyll serve --baseurl ''
url: "https://staging.cloudmouth.net"
{% endhighlight %}

The only remaining question is how to generate this file, which is going to vary from one branch to another. I was able to use the Amplify Console's environment variable support along with modified build settings to generate this file as a pre-build step:

![Jekyll and Amplify build settings]({{ site.baseurl }}/images/jekyll-amplify-build-settings.png)

The first line in the first red box checks for the existing or an environment variable called **Url**. If it doesn't exist, it will default it to https://cloudmouth.net. The next line then writes this to a file called **_config.env.yml**. In the second red box, I update the jekyll build command to read **_config.yml** (which is not enviornment specific) followed by **_config.env.yml** (which is environment specific). Settings from the latter, such as the environment-specific url, override settings from the former.

Finally, I set an environment variable per branch that gets picked up by this build script:

![Jekyll and Amplify environment variables]({{ site.baseurl }}/images/jekyll-amplify-env-vars.png)

## Tip 2 - Access control for non-production environments ##

## Tip 3 - Rewrites and redirects ##

Look for future posts possibly covering cloud to device interactions, different types of sensors and more.

Hope this post was helpful!