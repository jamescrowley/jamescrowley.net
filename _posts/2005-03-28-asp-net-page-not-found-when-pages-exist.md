---
id: 48
title: 'ASP.NET &#8220;Page Not Found&#8221; when pages exist&#8230;'
date: 2005-03-28T22:32:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/03/28/396100.aspx
permalink: /2005/03/28/asp-net-page-not-found-when-pages-exist/
sfw_comment_form_password:
  - 3Xeft9SkS9Zr
categories:
  - Uncategorized
---
I hit a strange problem today when I started getting the ASP.NET 404 error page whenever I tried to access my ASP.NET pages through IIS &#8211; whilst any non-ASP.NET pages in the same directory worked fine. After a bit of digging, it turned out that this was caused by me fiddling with the site&#8217;s &#8220;home directory&#8221; in IIS &#8211; I&#8217;d put it back to it&#8217;s original value, but with a trailing backslash&#8230; so

c:inetpubwwwroot <&#8211; this didn&#8217;t work

instead of

c:inetpubwwwroot&nbsp; <&#8211; this worked

I removed the trailing backslash, and everything was fine again. Very wierd!