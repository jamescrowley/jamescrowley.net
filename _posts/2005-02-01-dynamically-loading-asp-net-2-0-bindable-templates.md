---
id: 45
title: 'Dynamically loading ASP.NET 2.0 &#8220;Bindable&#8221; templates'
date: 2005-02-01T09:49:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/02/01/364426.aspx
permalink: /2005/02/01/dynamically-loading-asp-net-2-0-bindable-templates/
sfw_comment_form_password:
  - nr0dOGAkR6Tp
categories:
  - Uncategorized
---
**Update:** See <http://weblogs.asp.net/james_crowley/archive/2005/09/06/424539.aspx> 

Just found out that ASP.NET 2.0 doesn&#8217;t support dynamically loading &#8220;bindable&#8221; templates from a file; you&#8217;re restricted to writing them inline. In other words, if you&#8217;re using a FormView or DetailView control, and plan to use the new two-way data binding features, any use of LoadTemplate is out of the window. Plus the BindableTemplateBuilder (for creating a two-way data bound template on the fly) is currently an internal class. Which seems a big shame&#8230; (or maybe it&#8217;s just me??)

Does anyone know of any potential ways around this?

(Since finding this out, I&#8217;ve put in a request to at least [make BindableTemplateBuilder a public class here](http://lab.msdn.microsoft.com/ProductFeedback/viewFeedback.aspx?FeedbackId=d9c93c38-71c7-4fbd-9667-264a1456ba8f)).