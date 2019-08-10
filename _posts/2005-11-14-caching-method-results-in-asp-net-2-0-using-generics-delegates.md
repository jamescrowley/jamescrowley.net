---
id: 63
title: 'Caching Method Results in ASP.NET 2.0 using Generics &#038; Delegates'
date: 2005-11-14T20:42:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/11/14/430556.aspx
permalink: /2005/11/14/caching-method-results-in-asp-net-2-0-using-generics-delegates/
sfw_comment_form_password:
  - XkPGudwX6cCS
categories:
  - Uncategorized
---
Today I found myself coding a fairly familiar pattern &#8211; checking whether there was an entry in the ASP.NET cache with a particular key, if not, executing a method and adding the result to the cache, and either way, returning the result. 

I wondered whether there was a &#8220;nice&#8221; way to do this using generics and anonymous delegates. This is what I came up with&#8230;

`public delegate T MethodExecution<T>();<br />public static T GetCachedMethod<T>(string key, DateTime absoluteExpiration, MethodExecution<T> method)<br />{<br />&nbsp;&nbsp;&nbsp; if (HttpContext.Current.Cache[key] == null)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; HttpContext.Current.Cache.Insert(key,<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; method(),<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; null, absoluteExpiration, Cache.NoSlidingExpiration);</p>
<p>&nbsp;&nbsp;&nbsp; return (T)HttpContext.Current.Cache[key];<br />&nbsp;&nbsp;&nbsp; <br />}</p>
<p>`

Now, to use this method,I could write the following to return a cached (by one day) result of the method SomeMethodThatReturnsADataSet.

 `return GetCachedMethod<DataSet>(key,DateTime.Now.AddDays(1),<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; delegate() { return SomeMethodThatReturnsADataSet(myParam); });`

I&#8217;m not sure whether this just makes things more obscure &#8211; any comments? 🙂