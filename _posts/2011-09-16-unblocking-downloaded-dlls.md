---
id: 245
title: Unblocking downloaded DLLs
date: 2011-09-16T09:40:59+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=245
permalink: /2011/09/16/unblocking-downloaded-dlls/
sfw_comment_form_password:
  - 3Ex29vtkDVOy
categories:
  - General Computing
---
I find myself regularly forgetting to unblock zip files of various projects (when I&#8217;m not using NuGet) and then getting .NET errors around untrusted assemblies. To bulk unblock all files in a directory, simply

  * [Download the SysInternals &#8220;Streams&#8221; utility](http://technet.microsoft.com/en-us/sysinternals/bb897440.aspx). 
      * Then open a command prompt in the directory you want to fix up, and use the &#8220;-d&#8221; parameter:  
        `streams -d *.dll`</p> 
        Simples!