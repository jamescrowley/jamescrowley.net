---
id: 341
title: SSL Termination and Secure Cookies/requireSSL with ASP.NET Forms Authentication
date: 2014-03-07T23:37:21+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/323-autosave-v1/
permalink: /2014/03/07/323-autosave-v1/
---
If you&#8217;re running a HTTPS-only web application, then you probably have requireSSL set to true in your web.config like so:

<pre>&lt;httpCookies requireSSL="true" httpOnlyCookies="true"</pre>

With requireSSL set, any cookies ASP.NET sends with the HTTP response &#8211; in particular, the forms authentication cookies &#8211; will have the [&#8220;secure&#8221; flag](https://www.owasp.org/index.php/SecureFlag) set. This ensures that they will only be sent to your website when being accessed over HTTPS.

What happens if you put your web application behind a load balancer with SSL termination? In this case, ASP.NET will see the request coming in as non-HTTPS (Request.IsSecureConnection always returns false) and refuse to set your cookies:

## 

Fortunately, we have a few tricks up our sleeve.

We can take advantage of two things:

  1. If the HTTPS server variable is set to true, ASP.NET will think we are over HTTPS
  2. The HTTP\_X\_FORWARDED_PROTO header will contain the original protocol running at the load balancer (so we can check that the end connection is in fact HTTPS)

To do this, we take advantage of the rewrite module available in IIS 7 upwards:

<pre>&lt;rewrite&gt;
        &lt;rules&gt;
            &lt;rule name="HTTPS_AlwaysOn" patternSyntax="Wildcard"&gt;
                &lt;match url="*" /&gt;
                &lt;serverVariables&gt;
                    &lt;set name="HTTPS" value="on" /&gt;
                &lt;/serverVariables&gt;
                &lt;action type="None" /&gt;
                &lt;conditions&gt;
                    &lt;add input="{HTTP_X_FORWARDED_PROTO}" pattern="https" /&gt;
                &lt;/conditions&gt;
            &lt;/rule&gt;
        &lt;/rules&gt;
    &lt;/rewrite&gt;</pre>

You&#8217;ll also need to add HTTPS to the list of allowedServerVariables in the applicationHost.config (or through the URL Rewrite config)

<pre>&lt;rewrite&gt;
            &lt;allowedServerVariables&gt;
                &lt;add name="HTTPS" /&gt;
            &lt;/allowedServerVariables&gt;
        &lt;/rewrite&gt;</pre>

With thanks to [Levi Broderick](https://twitter.com/LeviBroderick) on the ASP.NET team who sent me in the right direction to this solution!