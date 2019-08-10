---
id: 93
title: 'PHP 5.3 and IIS 7 &#8211; beware of MySQL issues with IPv6'
date: 2010-09-06T08:02:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2010/09/06/php-5-3-and-iis-7-beware-of-mysql-issues-with-ipv6.aspx
permalink: /2010/09/06/php-5-3-and-iis-7-beware-of-mysql-issues-with-ipv6/
sfw_comment_form_password:
  - RsuVLh4iio02
categories:
  - PHP
---
I&#8217;ll keep this short and sweet as it&#8217;s [covered in depth elsewhere](http://blogs.iis.net/donraman/archive/2010/06/11/php-5-3-and-mysql-connectivity-problem.aspx). After installing PHP 5.3 suddenly my website stopped working &#8211; but instead of throwing errors, it simply sat there and eventually timed out (with no error). Turns out there are issues with both PHP and MySQL around IPv6. The most common solution is &#8220;turn off IPv6 support&#8221; &#8211; not a reassuring answer.

On the PHP front, the issue seems to be [around fsockopen](http://bugs.php.net/bug.php?id=51079) which works fine in 5.3.0 but not in 5.3.2. The major issue for most people though is that the MySQL driver doesn&#8217;t properly support IPv6 if you&#8217;re using a hostname (such as localhost) to connect instead of an IP address, particularly when localhost resolves to &#8220;::1&#8221;.

I&#8217;m not sure what changed in PHP 5.3.2 to bring this to the surface, but the solution is either to switch to using an IP address, for which there are [other good reasons to do so](http://www.ksingla.net/2010/06/impact-of-name-resolution-on-mysql_connect-perfomance/), or modify your hosts file (c:\windows\system32\drivers\etc) and change the line the starts with &#8220;::1 localhost&#8221; to &#8220;#::1 localhost&#8221;.

Hope this helps someone!