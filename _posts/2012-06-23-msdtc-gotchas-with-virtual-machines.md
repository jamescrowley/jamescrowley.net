---
id: 267
title: 'MSDTC gotcha&#8217;s with Virtual Machines'
date: 2012-06-23T18:24:17+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=267
permalink: /2012/06/23/msdtc-gotchas-with-virtual-machines/
sfw_comment_form_password:
  - cAhFlycLXlt6
categories:
  - Software Engineering
  - Uncategorized
---
Setting up some new infrastructure with a web and seperate db tier, I was hit with the usual MSDTC woes.

Error messages progressed bit by bit as I opened things up:

Attempt #1: _The partner transaction manager has disabled its support for remote/network transactions._

Attempt #2: _Network access for Distributed Transaction Manager (MSDTC) has been disabled. Please enable DTC for network access in the security configuration for MSDTC using the Component Services Administrative tool._

Attempt #3: _The MSDTC transaction manager was unable to push the transaction to the destination transaction manager due to communication problems. Possible causes are: a firewall is present and it doesn&#8217;t have an exception for the MSDTC process, the two machines cannot find each other by their NetBIOS names, or the support for network transactions is not enabled for one of the two transaction managers._

I couldn&#8217;t get past the final error though. [DTCPing](http://www.microsoft.com/en-us/download/details.aspx?id=2868) is a very useful tool if you&#8217;re struggling with this, along with this [TechNet article](http://technet.microsoft.com/en-us/library/cc753510%28WS.10%29.aspx) on what settings should be in place. One warning popped up that sent me in the right direction:

_WARNING:the CID values for both test machines are the same while this problem won&#8217;t stop DTCping test, MSDTC will fail for this_

As it happens, both machines were from an identical VM clone, and therefore had identical &#8220;CID&#8221; values. You can check this by going to HKEY\_CLASSES\_ROOT\CID. Look for the key that has a description of &#8220;MSDTC&#8221;.

Having found [Brian&#8217;s article](http://www.bryansgeekspeak.com/2011/02/windows-2008r2-msdtc-errors.html) who had done the hard work previously, this set me on my way &#8211; essentially you just need to uninstall and reinstall MSDTC on both of the machines. The following worked for me:

  1. Run &#8220;msdtc -uninstall&#8221; (from an admin prompt)
  2. Run &#8220;msdtc -install&#8221;
  3. Reconfigure MSDTC again from Component Services\My Computer\Distributed Transaction Coordinator\Local DTC (right click, properties)

And off you go&#8230; (don&#8217;t forget to enable the predefined DTC rules for local hosts in advanced firewall settings too)