---
id: 280
title: Configure Visual Studio 2012 to use 64 bit version of IIS Express
date: 2013-02-01T11:42:49+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=280
permalink: /2013/02/01/configure-visual-studio-2012-to-use-64-bit-version-of-iis-express/
sfw_comment_form_password:
  - m2DBEgXRncOf
categories:
  - DevOps
tags:
  - visual studio
---
By default Visual Studio (as a x86/32bit process) will always launch the 32bit version of IIS Express. If you have components that specifically require running under 64bit, you can can configure Visual Studio 2012 to use IIS Express x64 version by setting the following registry key:

> reg add HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\11.0\WebProjects /v Use64BitIISExpress /t REG_DWORD /d 1

You should note that this feature is not supported and has not been fully tested by Microsoft &#8211; you have been warned!