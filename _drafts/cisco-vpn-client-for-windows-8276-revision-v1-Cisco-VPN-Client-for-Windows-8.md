---
id: 353
title: Cisco VPN Client for Windows 8
date: 2014-03-07T23:48:50+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/276-revision-v1/
permalink: /2014/03/07/276-revision-v1/
---
There isn&#8217;t currently a version of Cisco&#8217;s VPN client that supports Windows 8, and after installation I received an error message complaining that the &#8220;VPN Client failed to enable virtual adapter.&#8221;.

Fortunately, there is a way to get this &#8220;legacy&#8221; VPN client to work, with a small registry change:

  * Open up the registry editor by typing regedit in Run prompt
  * Browse to the Registry Key HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\CVirtA
  * Edit the DisplayName entry and remove the leading characters from the value data upto &#8220;%;&#8221; i.e. 
      * For x86, change the value data from something like &#8220;@oem8.inf,%CVirtA_Desc%;Cisco Systems VPN Adapter” to &#8220;Cisco Systems VPN Adapter”
      * For x64, change the value data from something like &#8220;@oem8.inf,%CVirtA_Desc%;Cisco Systems VPN Adapter for 64-bit Windows” to &#8220;Cisco Systems VPN Adapter for 64-bit Windows”

Then you can try connecting again &#8211; this did the trick for me.