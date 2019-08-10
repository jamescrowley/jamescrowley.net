---
id: 85
title: 'Gotcha: &#8220;The specified metadata path is not valid.&#8221; with ADO.NET Entities on Vista x64'
date: 2008-01-26T16:09:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2008/01/26/gotcha-quot-the-specified-metadata-path-is-not-valid-quot-with-ado-net-entities-on-vista-x64.aspx
permalink: /2008/01/26/gotcha-quot-the-specified-metadata-path-is-not-valid-quot-with-ado-net-entities-on-vista-x64/
sfw_comment_form_password:
  - ePFkLcJmWCXm
categories:
  - Uncategorized
---
<P mce_keep="true">For those of you foolhardy enough to be running Vista x64 (myself included!), VS 2008, and the latest build of the ADO.NET Entities framework&#8230;&nbsp;you may well hit the following error message:

  
<P mce_keep="true">_The specified metadata path is not valid. A valid path must be either an existing directory, an existing file with extension &#8216;.csdl&#8217;, &#8216;.ssdl&#8217;, or &#8216;.msl&#8217;, or a URI that identifies an embedded resource._ </P><SPAN>The catch is that apparently the designers are not supported on 64-bit machines. The workaround is to copy two files from %windir%Microsoft.NETFramework64v3.5 to %windir%Microsoft.NETFrameworkv3.5:</SPAN><SPAN><br /> 

<UL>
  <br /> 
  
  <LI>
    <br /> <DIV align=left>Microsoft.Data.Entity.Build.Tasks.dll</DIV><br /> <LI>
      <br /> <DIV align=left>Microsoft.Data.Entity.targets</DIV>
    </LI></UL>
    <br /> <P align=left>then restart Visual Studio&nbsp;and rebuild your solution.</P></SPAN><br /> <P mce_keep="true"><SPAN>Thanks to Tommy Williams @ MSFT (<A class="" href="http://forums.microsoft.com/MSDN/ShowPost.aspx?PostID=2532468&SiteID=1" mce_href="http://forums.microsoft.com/MSDN/ShowPost.aspx?PostID=2532468&SiteID=1">found on the forums</A>)</P></SPAN></p>