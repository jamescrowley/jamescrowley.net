---
id: 79
title: 'Gotcha: HTTP_X_FORWARDED_FOR returns multiple IP addresses'
date: 2007-06-19T21:00:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2007/06/19/gotcha-http-x-forwarded-for-returns-multiple-ip-addresses.aspx
permalink: /2007/06/19/gotcha-http-x-forwarded-for-returns-multiple-ip-addresses/
sfw_comment_form_password:
  - TVcm8IZcOG01
categories:
  - Uncategorized
---
<P mce\_keep="true">I hit a small gotcha this evening. A visitor to Developer Fusion reported that they couldn&#8217;t gain access to the site at all, because our IP address detection logic was failing. We were checking the &#8220;HTTP\_X\_FORWARDED\_FOR&#8221; header for an IP address, before falling back to REMOTE_ADDR, turning the IP into a long integer, and doing an IP-to-country lookup in our database.&nbsp;Which seemed safe enough!

  
<P mce\_keep="true">As it turns out, HTTP\_X\_FORWARDED\_FOR can sometimes have a comma delimited list of IP addresses &#8211; so what we actually needed to be doing was take the last IP address in that list, before doing our conversion to an integer. </P>  
<P mce_keep="true">Thanks go out to Francois Botha, one of our visitors, for helping me track down this issue!</P></p>