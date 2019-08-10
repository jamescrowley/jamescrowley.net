---
id: 253
title: Determining if an assembly is x64 or x86
date: 2011-10-21T16:17:31+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=253
permalink: /2011/10/21/determining-if-an-assembly-is-x64-or-x86/
sfw_comment_form_password:
  - oZVplWr9Gb8o
categories:
  - Uncategorized
---
After encountering a strange deployment issue today, eventually it was tracked down to an x86 assembly being deployed to a x64 process. There&#8217;s a tool included with Visual Studio called corflags that was helpful here. Open up a Visual Studio command prompt, type corflags.exe assemblyname.dll and you&#8217;ll see something like this:

`Version   : v4.0.20926<br />
CLR Header: 2.5<br />
PE        : PE32<br />
CorFlags  : 11<br />
ILONLY    : 1<br />
32BIT     : 1<br />
Signed    : 1`

for a 32 bit assembly, and 

`Version   : v4.0.20926<br />
CLR Header: 2.5<br />
PE        : PE32<br />
CorFlags  : 9<br />
ILONLY    : 1<br />
32BIT     : 0<br />
Signed    : 1`

for a &#8220;Any CPU&#8221; assembly. There&#8217;s more details on everything these fields mean in [Brian Peek&#8217;s excellent blog post](http://theruntime.com/blogs/brianpeek/archive/2007/11/13/x64-development-with-net.aspx) on the topic.