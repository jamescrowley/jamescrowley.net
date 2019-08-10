---
id: 62
title: Re-setting Identity Column in SQL Server
date: 2005-11-10T00:11:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2005/11/10/430135.aspx
permalink: /2005/11/10/re-setting-identity-column-in-sql-server/
sfw_comment_form_password:
  - ktT9Ivvjkx0R
categories:
  - Uncategorized
---
Discovered something new today &#8211; normally I&#8217;d just use the TRUNCATE TABLE command in order to reset an identity column in a table within SQL Server. However, SQL Server doesn&#8217;t let you do this if you&#8217;ve got foreign key constraints pointing at the table; so instead, I deleted all the rows using a standard DELETE statement, and then reset the identity with the following command:

DBCC CHECKIDENT (_table_name_, RESEED, _new\_reseed\_value_)

According to the docs, &#8220;The current identity value is set to the _new\_reseed\_value_. If no rows have been inserted to the table since it was created, the first row inserted after executing DBCC CHECKIDENT will use _new\_reseed\_value_ as the identity. Otherwise, the next row inserted will use _new\_reseed\_value_ + 1. If the value of _new\_reseed\_value_ is less than the maximum value in the identity column, error message 2627 will be generated on subsequent references to the table.&#8221;

So, as we&#8217;ve already had rows in the table, I called the function with new\_reseed\_value&nbsp; to zero &#8211; hence the next row inserted into the table gets an identity of &#8220;1&#8221;. Nice!