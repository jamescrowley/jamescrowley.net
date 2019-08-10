---
id: 64
title: Caching Method Results in ASP.NET 2.0 using Delegates
date: 2005-11-14T22:17:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/11/14/430563.aspx
permalink: /2005/11/14/caching-method-results-in-asp-net-2-0-using-delegates/
sfw_comment_form_password:
  - Z8gUw2eBLwgy
categories:
  - Uncategorized
---
Hmm. Talk about over-engineering. I don&#8217;t think we really need generics at all, provided we&#8217;re happy with a cast outside the method instead of inside it. </p> 

`public delegate object MethodExecution();<br />public static object GetCachedMethod(string key, DateTime absoluteExpiration, MethodExecution method)<br />{<br />&nbsp;&nbsp;&nbsp; if (HttpContext.Current.Cache[key] == null)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; HttpContext.Current.Cache.Insert(key,<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; method(),<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; null, absoluteExpiration, Cache.NoSlidingExpiration);</p>
<p>&nbsp;&nbsp;&nbsp; return HttpContext.Current.Cache[key];<br />&nbsp;&nbsp;&nbsp; <br />}<br />...<br />``return (DataSet)GetCachedMethod(key, DateTime.Now.AddDays(1),<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; delegate() { return SomeMethodThatReturnsADataSet(myParam); });`