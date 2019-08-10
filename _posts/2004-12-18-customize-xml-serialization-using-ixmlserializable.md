---
id: 31
title: Customize XML Serialization using IXmlSerializable
date: 2004-12-18T00:24:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2004/12/18/323847.aspx
permalink: /2004/12/18/customize-xml-serialization-using-ixmlserializable/
sfw_comment_form_password:
  - oZX985N1SWfM
categories:
  - Uncategorized
---
[<font color="#002c99">XML Serialization in .NET</font>](http://www.developerfusion.com/show/3827/) provides an incredibly useful (and easy) way to turn objects into XML and back again. However, in some situations, you may need more control over how your object is serialized. Recently, I found myself needing to serialize an object that contained a number of property name/values &#8211; stored using a NameValueCollection. However, you can&#8217;t serialize this using the XmlSerializer class in .NET. I could have switched to using an array&nbsp;&nbsp;&#8211; at least for the serialization part &#8211; but this would have serialized the whole lot as a series of elements &#8211; and I wanted to do this using attributes. 

[Apologies &#8211; I&#8217;ve had to snip the code from here, as the layout kept messing around]

Read more here: [Customize XML Serialization using IXmlSerializable](http://www.developerfusion.com/show/4639/).