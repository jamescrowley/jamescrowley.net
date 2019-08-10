---
id: 41
title: Permanent 301 Redirect with QueryString in IIS
date: 2005-01-18T09:53:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/01/18/355060.aspx
permalink: /2005/01/18/permanent-301-redirect-with-querystring-in-iis/
sfw_comment_form_password:
  - g9AfZdkQpbiv
categories:
  - Uncategorized
---
If anyone&#8217;s ever tried to move domain, you&#8217;ll know its a pain. One way to make things a little easier is to provide an automatic 301 redirect from your old domain to your new one &#8211; this marks the new destination as a permanent change, and will generally be picked up by search engines. 

IIS provides an easy way to do this &#8211; but it&#8217;s not immediately obvious how to get it to forward querystrings too &#8211; for instance, if you wanted to forward developerfusion.com/show.aspx?id=20 to developerfusion.co.uk/show.aspx?id=20. Here&#8217;s now! (This works in both IIS 5 and IIS 6)

  1. Go into the IIS site properties for the domain you&#8217;re moving from. In the &#8220;Home Directory&#8221; tab, click the option &#8220;A redirection to a URL&#8221;. 
      * In the Redirect to box, enter the domain you wish to move to (no trailing slash), plus $S$Q &#8211; for example, http://www.developerfusion.com$S$Q 
          * Next, check the options that state the client will be sent to &#8220;The exact URL entered above&#8221;, and &#8220;A permanent redirection for this resource&#8221; </ol> 
        And that&#8217;s it! Now, what does this $S$Q do? These are basically tags that IIS will automatically replace &#8211; $S will be replaced with the subdirectory location (such as /images/show.aspx) and $Q will be replaced with the querystring (such as ?id=30). 
        
        You might be wondering why we can&#8217;t just use $Q, and then turn off &#8220;The exact URL entered above&#8221; &#8211; but in this situation,you get your domain name,then the query string, and \*then\* the subdirectory location &#8211; which probably isn&#8217;t what you wanted! For more information, [check out this](http://www.microsoft.com/resources/documentation/iis/6/all/proddocs/en-us/ref_we_redirect.mspx).