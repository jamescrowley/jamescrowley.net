---
id: 289
title: Achieving an A+ grading at Qualys SSL Labs (Forward Secrecy in IIS)
date: 2014-02-02T13:08:38+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=289
permalink: /2014/02/02/achieving-forward-secrecy-and-strict-transport-security-in-iis/
sfw_comment_form_password:
  - FoIp9k1ra5VB
categories:
  - IIS
  - 'Information Security &amp; Privacy'
tags:
  - ssl
---
At [FundApps](http://www.fundapps.co) we love the [SSL Labs](https://www.ssllabs.com/ssltest/) tool from Qualys for checking best practice on our SSL implementations. They recently announced a bunch of changes introducing [stricter security requirements](https://community.qualys.com/blogs/securitylabs/2014/01/21/ssl-labs-stricter-security-requirements-for-2014) for 2014, and a new A+ grade &#8211; so I was curious what it would take to achieve the new A+ grading. There are a few things required to now achieve A grading and then beyond:

  * TLS 1.2 required
  * Keys must be 2048 bits and above
  * Secure renegotiation
  * No RC4 on TLS 1.1 and 1.2 (RC4 has stuck around longer than it would be liked in order to mitigate the BEAST attack)
  * Forward secrecy for all browers that support it
  * HTTP Strict Transport Security with a long max age (Qualsys haven&#8217;t defined exactly what this is, but we use a 1 year value).

We&#8217;re using IIS so the focus of this entry is how to achieve an A+ grading in IIS 7/8.

**Forward Secrecy & Best Practice Ciphers**

Attention to Forward Secrecy [has been increasing in recent time](https://www.eff.org/deeplinks/2013/08/pushing-perfect-forward-secrecy-important-web-privacy-protection) &#8211; the key benefit being if, say, the NSA obtain your keys in the future, this will not compromise previous communications that were encrypted using session keys derived from your long term key.

To set up support for Forward Secrecy, the easiest approach (in a Windows/IIS world) is to download the latest version of the [IIS Crypto](https://www.nartac.com/Products/IISCrypto/) tool. This makes it really easy to get your SSL Ciphers in the right order and the correct ones enabled rather than messing directly with the registry.

Once downloaded, if you click the &#8216;Best Practice&#8217; option, this will enable ECHDE as the preferred cipher (required for forward secrecy). The tool does also keep SSL 3.0, RC4 and 3DES enabled in order to support IE 6 on Windows XP. If you don&#8217;t require this, you can safely disable SSL 3.0, TLS\_RSA\_WITH\_RC4\_128\_SHA and TLS\_RSA\_WITH\_3DES\_EDE\_CBC_SHA in the cipher list. We also disable MD5.

**HTTP Strict Transport Security**

The other part of the equation is enabling a [HTTP Strict Transport](http://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) header. The idea with this is to stop man-in-the-middle attacks whereby they transparently convert a secure HTTPS connection to a plain HTTP connection. Visitors can see the connection is insecure, but there is no way of knowing that the connection \*should\* have been secure. By adding a HTTP Strict Transport header (which is remembered by the browser and stored for a specified period), then provided first communication with the server is not tampered with (by stripping out the header), the browser will prevent non-secure communication from then on.

Doing this is simple &#8211; but you need to ensure that you only return a Strict-Transport-Security header on a HTTPS connection. Any requests on HTTP should \*not\* have this header, and should be 301 redirect-ing to the HTTPS version. especially if your website only responds to HTTPS requests in the first place (we use a seperate website to redirect from non-HTTPS requests).

In our case, we have a seperate website already responsible for the non-HTTP redirection, so it was simply a case of adding the following in our system.webServer section of the web.config

 ``

    <system.webServer>
      <httpProtocol>
        <customHeaders>
           <add name="Strict-Transport-Security" value="max-age=31536000" />
        </customHeaders>
      </httpProtocol>
    </system.webServer>

 ``

If you have to deal with both HTTPS and non-HTTPS, then [implementation section on WikiPedia](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security#Implementation) gives an example of how.

The end result? An A+ grading from the SSL Labs tool.