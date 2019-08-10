---
id: 361
title: 'Beware: Upgrade to ASP.NET MVC 2.0 with care if you use AntiForgeryToken'
date: 2014-03-07T23:58:24+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/92-revision-v1/
permalink: /2014/03/07/92-revision-v1/
---
If you&#8217;re thinking of upgrading to MVC 2.0, and you take advantage of the AntiForgeryToken support then be careful &#8211; you can easily kick out all active visitors after the upgrade until they restart their browser. Why&#8217;s this?  
For the anti forgery validation to take place, ASP.NET MVC uses a session cookie called &#8220;\_\_RequestVerificationToken\_Lw\\_\_&#8221;.

This gets checked for and de-serialized on any page where there is an AntiForgeryToken() call. However, the format of this validation cookie has apparently changed between MVC 1.0 and MVC 2.0.  
What this means is that when you make to switch on your production server to MVC 2.0, suddenly all your visitors session cookies are invalid, resulting in calls to AntiForgeryToken() throwing exceptions (even on a standard GET request) when de-serializing it:

`[InvalidCastException: Unable to cast object of type 'System.Web.UI.Triplet' to type 'System.Object[]'.]<br />
System.Web.Mvc.AntiForgeryDataSerializer.Deserialize(String serializedToken) +104`

 ``

`[HttpAntiForgeryException (0x80004005): A required anti-forgery token was not supplied or was invalid.]<br />
System.Web.Mvc.AntiForgeryDataSerializer.Deserialize(String serializedToken) +368<br />
System.Web.Mvc.HtmlHelper.GetAntiForgeryTokenAndSetCookie(String salt, String domain, String path) +209<br />
System.Web.Mvc.HtmlHelper.AntiForgeryToken(String salt, String domain, String path) +16<br />
System.Web.Mvc.HtmlHelper.AntiForgeryToken() +10<br />
<snip>`

So you&#8217;ve just kicked all your active users out of your site with exceptions until they think to restart their browser (to clear the session cookies).

The only work around for now is to either write some code that wipes this cookie &#8211; or disable use of AntiForgeryToken() in your MVC 2.0 site until you&#8217;re confident all session cookies will have expired. That in itself isn&#8217;t very straightforward, given how frequently people tend to hibernate/standby their machines &#8211; the session cookie will only clear once the browser has been shut down and re-opened.

Hope this helps someone out there!