---
id: 585
title: Statistics causing long delays
date: 2018-07-12T13:58:48+00:00
author: james.crowley
layout: post
guid: https://www.jamescrowley.net/?p=585
permalink: /?p=585
categories:
  - Uncategorized
---
THE PLOT HAS THICKENED, here&#8217;s the story:

jon and I spent a while today poking around with the AQR prod DB trying to definitively pin down the cause of the slow portfolio viewer.

Firstly he clicked around a bit. The first entity to load was slow (minutes), but after that point it was relatively fast (5-15s) even when switching dates. It also appeared that whilst the slow query was executing IO was nowhere near under pressure. This suggested that loading the data off disk and into memory wasn&#8217;t the cause of the slow down. We confirmed this by doing a \`DBCC DROPCLEANBUFFERS\` which should clear out any data being held in memory, followed by a query to confirm the buffers on the instance had been emptied. Querying again showed a similar performance to the &#8220;fast&#8221; operations, and not a slow operation of minutes. This confirmed to us that disk IO was not the bottleneck.

Next we turned our eyes on the plans. We hypothesised that a different plan was being used for the first operation compared to subsequent operations, this would line up with the first operation being slow but subsequent ones being fast independent of parameters. The counter to this is that we are using sp\_executesql which \_should_ just cache the first plan it has against the query and never try and optimise further. We decided to check anyway. So we ran \`DBCC FREEPROCCACHE\` to clear out any cached query plans there may be against the query. It still ran quickly. So we did the same again and cleaned the buffers out at the same time. Still ran quickly. Plans were not the culprit. :philosoraptor:

So at this point we were fairly certain that the slowness had nothing to do with disk IO or plan cache. After a few minutes of scratching heads we decided to go looking at the stats. We hypothesised that on the first query after an engine run SQL was updating its stats so it could decide whether or not the plan it had in cache was a good one or not. SQL will do this when it feels sufficient data has changed within the table since it made the plan that the stats it was based on may be out of date. Exactly when SQL decides stats are out of date is a bit of a secret algorithm, but it&#8217;s essentially a % of the data on the table having changed which changes depending on the size of the table.

So we ran a query against the \`PortfolioStore_AggregationAsset\` table&#8217;s stats to see if any had last been updated around the time of our slow query. It looked like the one for \`ParentAssetId\` had been updated at the time we ran the query. This lines up as \`ParentAssetId IS NULL\` is a condition in the standard portfolio viewer query.  
So we decided to play fast and loose in prod and dropped that set of statistics. In theory the optimiser would rebuild that statistic when it saw the query so it could come up with a plan. We ran the query again. :tumbleweed: it was slow once more. It took roughly the same amount of time as the original slow query.

So now that seems to be the leading hypothesis. Statistics updates on a first query being done by the optimiser before it decides on a plan. However, we need more proof that that is the case. I&#8217;m now looking into putting some logging in place that will report the stats update time of the portfolio tables after a long running portfolio store query. This should then be able to tell us if it is the culprit.

If it IS the culprit, there are several things we can do about it. Ranging from crazy intervention to light touch. On the crazy intervention side we can for SQL to use a specific plan for the portoflio store queries. This is a bit overkill, and given the combinations in different filters that can be provided is likely to be complicated and a bad move. Another approach we can take is forcing SQL to update statistics on these tables at the end of an engine run. It should only take a couple of minutes, and if it is going to do it on the first query it sees anyway it is probably better to get it out of the way before a query comes in.  
there are probably other avenues we could look into with being explicit over the definition of the table statistics rather than leaving it to Auto\_update\_stats. But slack told me my message was too long so i&#8217;m going to stop now and get on with proving this is the issue

matt [7:00 AM]  
Had a poke around with putting the logging in somewhere. As it has to call off another SQL query it didn&#8217;t really fit in anywhere. It&#8217;s also a bank holiday weekend here so I won&#8217;t be on until tuesday. For interest the query is:

&#8220;\`SELECT name AS stats_name,  
STATS\_DATE(object\_id, stats\_id) AS statistics\_update_date  
FROM sys.stats  
WHERE object\_id = OBJECT\_ID(&#8216;PortfolioStore_AggregationAsset&#8217;);&#8220;\`

If anyone feels like poking AQR prod again after their next run and running that afterwards (and also against the asset table) then be my guest

james [8:35 AM]  
Really interesting @matt 🙂

matt [10:59 PM]  
i&#8217;ve thrown up a PR that updates statistics on these tables at the end of an engine run (https://github.com/fundapps/Rapptr/pull/6507) this happens after the job has been marked as complete, so shouldn&#8217;t affect user visible times etc

From what i could tell the other option we had would be to switch on AUTO\_UPDATE\_STATISTICS_ASYNC. For the portfolio store that would probably be OK, but the switch is DB wide and wouldn&#8217;t want to do it without properly understanding the impacts across all the tables. It may be something we choose to investigate and do, my initial feeling is that it would be fine to be on across the DB but :shrug: without testing it properly. (edited)