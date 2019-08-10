---
id: 260
title: Debugging InstallUtil service installation
date: 2012-01-17T18:59:37+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=260
permalink: /2012/01/17/debugging-installutil-service-installation/
sfw_comment_form_password:
  - mqUwommqviDI
categories:
  - Uncategorized
---
If you&#8217;re using the Installer class and either InstallUtil or calling the helper methods directly, you might want to attach the debugger to actually track down problems with the code. One simple line:

<pre>System.Diagnostics.Debugger.Launch();</pre>

will then launch a prompt to pick a debugger to step into the problem code.