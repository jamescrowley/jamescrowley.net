---
id: 65
title: Multiple CSS Classes
date: 2005-11-17T14:10:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/11/17/430802.aspx
permalink: /2005/11/17/multiple-css-classes/
sfw_comment_form_password:
  - jS8MxCdwshe6
categories:
  - Uncategorized
---
I&#8217;m not sure I should really admit to not knowing this&#8230; but today was the first time I&#8217;d realised that the &#8220;class&#8221; attribute for elements within a HTML document can accept more than one class name. doh.

**Update:** Here&#8217;s a simple example. With the following in your style sheet&#8230;

`.align-c { text-align: center; }<br />.font-b { font-weight: bold; }`

You can have something like <div class=&#8221;align-c font-b&#8221;></div> to make the contents of the div tag bold and centre-aligned.