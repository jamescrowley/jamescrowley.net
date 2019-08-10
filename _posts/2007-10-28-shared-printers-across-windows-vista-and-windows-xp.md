---
id: 84
title: Shared Printers across Windows Vista and Windows XP
date: 2007-10-28T14:50:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2007/10/28/shared-printers-across-windows-vista-and-windows-xp.aspx
permalink: /2007/10/28/shared-printers-across-windows-vista-and-windows-xp/
sfw_comment_form_password:
  - pFpDXPojH0RW
categories:
  - Uncategorized
---
<P mce_keep="true">Since getting a shiny new machine running Vista, I&#8217;d been having a bit of grief trying to get it to print to my Canon i6500&nbsp;printer shared through another Windows XP machine. Vista has built-in support for the printer (running locally), but when&nbsp;trying to add it across a network, as the XP machine could not supply the correct 64bit Vista drivers, the&nbsp;Vista machine&nbsp;wasn&#8217;t too happy &#8211; pointing to the correct location of the local device drivers didn&#8217;t help either!

  
<P mce_keep="true">After sifting through various solutions &#8211; this was the one that worked for me.</P>  
<P mce_keep="true">On the Vista machine,&nbsp;</P>  
<P mce_keep="true">&#8211; Choose to add a local printer  
&#8211; Create a new local port, and set its name so it matches the network share ([\servername](file://server/name))  
&#8211; Manually select the appropriate printer driver from the automatically supported set (or select an appropriate vista driver)</P>  
<P mce_keep="true">This then tricks Vista into thinking we have a local printer &#8211; so it can install the correct drivers &#8211; that actually redirects to the network printer.</P>  
<P mce_keep="true">I think this should work in the reverse direction too, if Windows XP is geting upset printing to a device shared through Vista.</P>  
<P mce_keep="true">Hope this helps someone!</P></p>