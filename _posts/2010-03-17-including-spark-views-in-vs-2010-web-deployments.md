---
id: 91
title: Including Spark views in VS 2010 web deployments
date: 2010-03-17T19:29:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2010/03/17/including-spark-views-in-vs-2010-web-deployments.aspx
permalink: /2010/03/17/including-spark-views-in-vs-2010-web-deployments/
sfw_comment_form_password:
  - WR58hnj1veN0
categories:
  - ASP.NET
  - Web Development
---
Visual Studio 2010 includes much improved deployment tools &#8211; but by default it only includes files &#8220;needed to run this application&#8221;. If you&#8217;re using the Spark view engine for ASP.NET MVC, then the Spark views aren&#8217;t considered one of them!

The trick is to ensure your .spark views have a build action of &#8220;Content&#8221; instead of the default &#8220;None&#8221;. Clearly remembering this each time would get somewhat tedious, so instead you can add the following registry entries:

    
    
    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINESOFTWAREMicrosoftVisualStudio10.0Projects{F184B08F-C81C-45f6-A57F-5ABD9991F28F}FileExtensions.spark]
    "DefaultBuildAction"="Content"
    
    [HKEY_LOCAL_MACHINESOFTWAREMicrosoftVisualStudio10.0Projects{FAE04EC0-301F-11d3-BF4B-00C04F79EFBC}FileExtensions.spark]
    "DefaultBuildAction"="Content"
    

(just add this to a .reg file and run it)