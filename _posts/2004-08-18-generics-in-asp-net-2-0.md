---
id: 27
title: Generics in ASP.NET 2.0?
date: 2004-08-18T09:51:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2004/08/18/216420.aspx
permalink: /2004/08/18/generics-in-asp-net-2-0/
sfw_comment_form_password:
  - V27NGGkzrwCz
categories:
  - Uncategorized
---
I&#8217;ve finally gotten around to having a closer look at the current Whidbey beta &#8211; particularly from the ASP.NET angle. However, I&#8217;ve got a&nbsp;fairly straightforward&nbsp;question that so far I haven&#8217;t been able to find any answer to &#8211; or even the slightest hint of one. Suppose I want to create a custom Web Control, that accepts a type parameter so that, for instance, we can provide a strongly typed DataItem property. 

Obviously you can create an instance of this object by doing

 <font color="#0000ff"></p> 

<p>
  this</font>.Controls.Add(<font color="#0000ff">new</font> <font color="#008080">SomeGenericControl</font><<font color="#008080">SomeObjectType</font>>());
</p>

<p>
  in the page load event, but at the moment there doesn&#8217;t seem to be a way to define this in the aspx page, along the lines of</font>
</p>

<p>
  <font color="#0000ff"> </p> 
  
  <p>
    <</font><font color="#800000">vc</font><font color="#0000ff">:</font><font color="#800000">SomeGenericControl</font><font color="#ff00ff"> : </font><font color="#ff0000">SomeObjectType</font><font color="#ff00ff"> </font><font color="#ff0000">runat</font><font color="#0000ff">=&#8221;server&#8221;</font><font color="#ff00ff"> </font><font color="#0000ff">/></font><font size=""> </p> 
    
    <p>
      </font>
    </p>
    
    <p>
      or equivalent. Is this something that just hasn&#8217;t made the current beta, or is that syntax not going to be included at all?
    </p>