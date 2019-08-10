---
id: 429
title: Get ASP.NET auth cookie using PowerShell (when using AntiForgeryToken)
date: 2018-01-13T17:54:41+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.co.uk/2018/01/13/315-revision-v1/
permalink: /2018/01/13/315-revision-v1/
---
At [FundApps](http://www.fundapps.co) we run a regular [SkipFish](https://code.google.com/p/skipfish/) scan against our application as one of our tools for monitoring for security vulnerabilities. In order for it to test beyond our login page, we need to provide a valid .ASPXAUTH cookie (you&#8217;ve renamed it, right?) to the tool.

Because we want to prevent Cross-site request forgeries to our login pages, we&#8217;re using the [AntiForgeryToken](http://blog.stevensanderson.com/2008/09/01/prevent-cross-site-request-forgery-csrf-using-aspnet-mvcs-antiforgerytoken-helper/) support in MVC. This means we can&#8217;t just post our credentials to the login url and fetch the cookie that is returned. So here&#8217;s the script we use to fetch a valid authentication cookie before we call SkipFish with its command line arguments:

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/9152282">Gist</a>.
  </noscript>
</div>