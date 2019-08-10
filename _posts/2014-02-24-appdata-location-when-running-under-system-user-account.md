---
id: 320
title: AppData location when running under System user account
date: 2014-02-24T12:48:32+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=320
permalink: /2014/02/24/appdata-location-when-running-under-system-user-account/
sfw_comment_form_password:
  - iD0FFUw0GmTc
categories:
  - DevOps
tags:
  - windows
---
As it took far too much Googling to find this, if you need to access the AppData folder for the System account, go here:

C:\Windows\System32\config\systemprofile\AppData\Local  
C:\Windows\SysWOW64\config\systemprofile\AppData\Local  
I hit this because we needed to clear the NuGet package cache for a TeamCity build agent which was running as a service under the System account.