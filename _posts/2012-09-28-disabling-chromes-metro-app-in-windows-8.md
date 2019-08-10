---
id: 274
title: 'Disabling Chrome&#8217;s Metro app in Windows 8'
date: 2012-09-28T10:04:00+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=274
permalink: /2012/09/28/disabling-chromes-metro-app-in-windows-8/
sfw_comment_form_password:
  - kd5UKAv4hZV7
categories:
  - General Computing
---
At time of writing, if you replace IE with Chrome on Windows 8 then Chrome installs both a desktop and a Metro version of itself. Personally, as most of my time is spent in the desktop, I&#8217;d rather Chrome just always opened there.

There&#8217;s currently an [open issue on the chromium website](http://code.google.com/p/chromium/issues/detail?id=140347), but in the meantime there&#8217;s a relatively simple workaround. You just need to open up regedit, navigate to

HKEY\_CLASSES\_ROOT\ChromeHTML\shell\open\command

and then rename/remove the DelegateExecute entry. Then Chrome will always open in desktop mode &#8211; problem solved!