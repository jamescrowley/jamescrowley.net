---
id: 88
title: UrlRewriting, .NET 2.0 SP1 and Search Engines
date: 2008-05-26T17:05:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2008/05/26/urlrewriting-net-2-0-sp1-and-search-engines.aspx
permalink: /2008/05/26/urlrewriting-net-2-0-sp1-and-search-engines/
sfw_comment_form_password:
  - xcNAM7WD6RcY
categories:
  - Uncategorized
---
Having been caught out by this issue once again this weekend, I thought I&#8217;d better blog about it so I don&#8217;t scratch my head searching around again for a third time!

If you&#8217;ve been getting some wierd &#8220;Cannot use a leading .. to exit above the top directory.&#8221; exceptions occuring on your site (you \*do\* log those, don&#8217;t you?), that you can&#8217;t reproduce in the browser, stay tuned. The issue crops up with URL Rewriting in .NET 2 SP1 &#8211; and the reason I&#8217;ve hit this again is when our production server was upgraded to .NET 3.5&#8230; evidentally this installed the service pack as a side-effect. So much for our patching strategy.

Anyway, this triggered a flow of errors for &#8220;Cannot use a leading .. to exit above the top directory.&#8221;, all stemming back to a call to System.Web.Util.UrlPath.ReduceVirtualPath &#8211; but apparently only for particular visitors to the site &#8211; specifically search engine bots, including Googlebot. The issue occurs, as far as I understand, because .NET is specifically targeting code to particular browsers &#8211; in this case, I believe the issue results because it knows the user-agent doesn&#8217;t support cookies, and is therefore trying to work accordingly.

There are two workarounds out there.

1. In your web.config,add the following:

`<authentication mode="Forms"><br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <forms cookieless="UseCookies" /><br /></authentication>`

This will bypass the issue entirely &#8211; by telling .NET to always use cookies for authentication &#8211; but if you require forms authentication to work in a cookie-less scenario,then this won&#8217;t work. So, on to option number 2

2. Create a .browser file to match the user agents that are causing the issue. <a href="http://todotnet.com/archive/0001/01/01/7472.aspx" mce_href="http://todotnet.com/archive/0001/01/01/7472.aspx">Check out this article that describes how</a>.  
&nbsp;