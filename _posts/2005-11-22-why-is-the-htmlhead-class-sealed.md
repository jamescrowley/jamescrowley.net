---
id: 67
title: Why is the HtmlHead class sealed?
date: 2005-11-22T23:27:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/11/22/431293.aspx
permalink: /2005/11/22/why-is-the-htmlhead-class-sealed/
sfw_comment_form_password:
  - Zc9SKkh4ILEg
categories:
  - Uncategorized
---
ASP.NET 2.0 gives us a Page.Title property, which we can set in code, or in the Page directive. Great! Unfortunately, I had a requirement so that whilst I&#8217;d be setting a portion of the title from the page, the rest would be pre-defined (ideally within the master page that I use). Obviously you can&#8217;t fiddle the stuff in the server-side title tag, because the contents just gets overriden. So I thought I&#8217;d just extend the new System.Web.UI.HtmlControls.HtmlHead class and fiddle the Title property to add the required string to the end Except you can&#8217;t. Because its sealed. Why? And does anyone have another way to do this?