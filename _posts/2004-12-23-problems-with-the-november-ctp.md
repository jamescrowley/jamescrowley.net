---
id: 32
title: Problems with the November CTP
date: 2004-12-23T18:15:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2004/12/23/331361.aspx
permalink: /2004/12/23/problems-with-the-november-ctp/
sfw_comment_form_password:
  - cO97c8FN96AH
categories:
  - Uncategorized
---
I&#8217;ve finally managed to get hold of a copy of the November CTP, but have hit a strange problem when editing any web.config file &#8211; with a message box stating &#8220;Method not found: &#8216;Int32 Microsoft.VisualStudio. TextManager.Interop. IVsTextViewEx.IsExpansionUIActive()&#8217;.&#8221; whenever I try to cut/paste/delete (and trying to type in the file just doesn&#8217;t do anything). The other source files seem to edit fine. Has anyone else experienced this, or got any clue as to what might be causing it?

Also, has anyone experienced problems with the solution explorer jumping to the top of the project when you&#8217;ve got multiple projects and switch between source files in different projects? (See [this bug report](%20http://lab.msdn.microsoft.com/productfeedback/viewfeedback.aspx?feedbackid=d25762c2-447b-47ab-be71-059ee904325c%20) ) I&#8217;ve been experiencing that both in Beta 1, and in the November CTP &#8211; Microsoft haven&#8217;t been able to repro it yet, but 10 people have validated the bug so far&#8230;