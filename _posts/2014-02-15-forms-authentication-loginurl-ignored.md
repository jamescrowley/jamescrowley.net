---
id: 299
title: Forms Authentication loginUrl ignored
date: 2014-02-15T13:44:33+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=299
permalink: /2014/02/15/forms-authentication-loginurl-ignored/
sfw_comment_form_password:
  - P08qC1pOdmfF
categories:
  - ASP.NET
  - 'Information Security &amp; Privacy'
  - Web Development
tags:
  - forms authentication
  - security
---
I hit this issue a while back, and someone else just tripped up on it so thought it was worth posting here. If you&#8217;ve got loginUrl in your Forms Authentication configuration in web.config set, but your ASP.NET Forms or MVC app has suddenly started redirecting to ~/Account/Login for no apparent reason, then the new simpleMembership(ish) provider is getting in the way. This seems to happen after updating the MVC version, or installing .NET 4.5.1 at the moment.

Try adding the following to your appSettings in the web.config file:

    <add key="enableSimpleMembership" value="false"/>

which resolved the issue for me. Still trying to figure out with Microsoft why this is an issue.