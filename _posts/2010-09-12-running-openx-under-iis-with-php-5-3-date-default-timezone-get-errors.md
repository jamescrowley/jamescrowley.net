---
id: 94
title: 'Running OpenX under IIS with PHP 5.3 &#8211; date_default_timezone_get errors'
date: 2010-09-12T21:50:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2010/09/12/running-openx-under-iis-with-php-5-3-date-default-timezone-get-errors.aspx
permalink: /2010/09/12/running-openx-under-iis-with-php-5-3-date-default-timezone-get-errors/
sfw_comment_form_password:
  - RQ5DwkKjmFjn
categories:
  - PHP
  - Software Engineering
---
I run OpenX (an open source ad serving platform) under IIS and PHP &#8230;. but after upgrading to PHP 5.3 noticed the following error appearing in the PHP error logs file (c:\windows\temp\php-errors.log by default).

`<br />
PHP Warning:  date_default_timezone_get(): It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected 'Europe/London' for '1.0/DST' instead in C:\inetpub\wwwroot\openx-2.8.5\www\delivery\spc.php on line 191`

The solution to make this error go away is simply to specify a default timezone in the php.ini configuration file. In my case this looked like:

<pre>[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
date.timezone = "Europe/London"
</pre>