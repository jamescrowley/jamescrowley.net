---
id: 87
title: MasterPages, ViewState and web.config files
date: 2008-02-05T13:04:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2008/02/05/masterpages-viewstate-and-web-config-files.aspx
permalink: /2008/02/05/masterpages-viewstate-and-web-config-files/
sfw_comment_form_password:
  - OCL6xnH7qjqc
categories:
  - Uncategorized
---
protected override void OnInit(EventArgs e)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // we use this so that we can set the enableViewState property in the web.config  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // although it sets it at the page level, it doesn&#8217;t pass it on to the master page  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; this.EnableViewState = this.Page.EnableViewState;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base.OnInit(e);  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }