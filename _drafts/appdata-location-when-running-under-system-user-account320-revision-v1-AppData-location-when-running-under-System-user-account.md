---
id: 352
title: AppData location when running under System user account
date: 2014-03-07T23:47:00+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/320-revision-v1/
permalink: /2014/03/07/320-revision-v1/
---
As it took far too much Googling to find this, if you need to access the AppData folder for the System account, go here:

C:\Windows\System32\config\systemprofile\AppData\Local  
C:\Windows\SysWOW64\config\systemprofile\AppData\Local  
I hit this because we needed to clear the NuGet package cache for a TeamCity build agent which was running as a service under the System account.