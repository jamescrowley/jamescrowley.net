---
id: 360
title: Detecting 404 errors after a new site design
date: 2014-03-07T23:58:24+00:00
author: james.crowley
layout: revision
guid: http://www.jamescrowley.co.uk/2014/03/07/98-revision-v1/
permalink: /2014/03/07/98-revision-v1/
---
We recently re-designed [Developer Fusion](http://www.developerfusion.com/) and as part of that we needed to ensure that any external links were not broken in the process. In order to monitor this, we used the awesome [LogParser](http://www.microsoft.com/DownLoads/details.aspx?FamilyID=890cd06b-abf8-4c25-91b2-f8d975cf8c07) tool. All you need to do is open up a command prompt, navigate to the directory with your web site&#8217;s log files in, and run a query like this:

`"c:\program files (x86)\log parser 2.2\logparser" "SELECT top 500 cs-uri-stem,COUNT(*) as Computed FROM u_ex*.log WHERE sc-status=404 GROUP BY cs-uri-stem order by COUNT(*) as Computed desc" -rtp:-1 > topMissingUrls.txt<br />
` 

And you&#8217;ve got a text file with the top 500 requested URLs that are returning 404. Simple!