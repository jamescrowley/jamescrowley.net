---
id: 95
title: web.config transformations for NHibernate
date: 2011-01-19T12:22:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2011/01/19/web-config-transformations-for-nhibernate.aspx
permalink: /2011/01/19/web-config-transformations-for-nhibernate/
sfw_comment_form_password:
  - gS39SNXP4pUw
categories:
  - NHibernate
  - Software Engineering
---
If you&#8217;re trying to use web.config transformations in VS 2010 with nHibernate you might be hitting the same issue I&#8217;ve been getting the transformation to match the hibernate-configuration node. The reason is because you have to specify an `xmlns="urn:nhibernate-configuration-2.2"` attribute as part of the configuration:

<pre>&lt;configuration&gt;
&lt;hibernate-configuration xmlns="urn:nhibernate-configuration-2.2"&gt;
    &lt;session-factory&gt;
      &lt;property name="connection.connection_string"&gt;connection string&lt;/property&gt;
      ...
    &lt;/session-factory&gt;
  &lt;/hibernate-configuration&gt;
&lt;/configuration&gt;
</pre>

To fix, just set the namespace on the target node in the transformation file like this: 

<pre>&lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"
               xmlns:hib="urn:nhibernate-configuration-2.2"&gt;
  &lt;hib:hibernate-configuration&gt;
    &lt;hib:session-factory&gt;
      &lt;hib:property xdt:Transform="Replace" xdt:Locator="Match(name)" name="connection.connection_string"&gt;your connection string&lt;/hib:property&gt;
    &lt;/hib:session-factory&gt;
  &lt;/hib:hibernate-configuration&gt;
&lt;/configuration&gt;
</pre>