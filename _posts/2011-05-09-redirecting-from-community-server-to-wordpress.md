---
id: 192
title: Redirecting from Community Server to WordPress
date: 2011-05-09T12:29:30+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=192
permalink: /2011/05/09/redirecting-from-community-server-to-wordpress/
sfw_comment_form_password:
  - PgLwkkzMiXVJ
categories:
  - Uncategorized
---
Just a quick note &#8211; if you switch from Community Server to WordPress like I have, in order to keep your links working you can add a simple regex rewrite rule to IIS. I simply used the following:

`^/james_crowley/archive/(.*).aspx$<br />
http://www.jamescrowley.co.uk/$1/`

and

`^/james_crowley/(.*)$<br />
http://www.jamescrowley.co.uk/$1`

where /james_crowley/ was where my blog was installed previously (on weblogs.asp.net as it happens).