---
id: 264
title: 'Migrating old websites &#038; Rewrite maps in IIS 7'
date: 2012-06-23T11:59:03+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=264
permalink: /2012/06/23/migrating-old-websites-rewrite-maps-in-iis-7/
sfw_comment_form_password:
  - kQRMpz3DiQAD
categories:
  - IIS
  - Uncategorized
---
If you&#8217;re migrating to a new website and need to map old IDs to new IDs, I&#8217;ve just discovered that the UrlRewrite plugin in IIS has a great feature I hadn&#8217;t come across before called rewriteMaps. This means instead of writing a whole bunch of indentical looking rewrite rules, you can write one &#8211; and then simply list the ID mappings.

The syntax of the RegEx takes a bit of getting used to, but in our case we needed to map

/(various|folder|names|here)/display.asp?id=[ID]

to a new website url that looked like this:

/show/[NewId]

You can define a rewriteMap very simply &#8211; most examples I saw included full URLs here, but we just used the ID maps directly:

<pre>&lt;rewriteMaps&gt;
  &lt;rewriteMap name="Articles"&gt;
    &lt;add key="389" value="84288" /&gt;
    &lt;add key="525" value="114571" /&gt;
    &lt;add key="526" value="114572" /&gt;
  &lt;/rewriteMap&gt;
&lt;/rewriteMaps&gt;</pre>

You can reference a rewriteMap using {MapName:{SomeCapturedValue}}, so if SomeCapturedValue equalled 525 then you&#8217;d get back 114571 in the list above.

Because we&#8217;re looking to match a querystring based id, and you can&#8217;t match queryString parameters in the primary match clause, we needed to add a condition, and then match on that captured condition value instead, using an expression like this:

<pre>http://www.newdomain.com/show/{Articles:{C:1}}/</pre>

The final rule XML follows:

<pre>&lt;rule name="Redirect rule for Articles" stopProcessing="true"&gt;
  &lt;match url="(articles|java|dotnet|xml|databases|training|news)/display\.asp" /&gt;
  &lt;conditions&gt;
    &lt;add input="{QUERY_STRING}" pattern="id=([0-9]+)" /&gt;
  &lt;/conditions&gt;
  &lt;action type="Redirect" url="http://www.developerfusion.com/show/{Articles:{C:1}}/" appendQueryString="false" /&gt;
&lt;/rule&gt;</pre>