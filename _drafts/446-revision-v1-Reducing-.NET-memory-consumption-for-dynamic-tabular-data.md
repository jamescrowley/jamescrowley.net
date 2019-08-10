---
id: 480
title: Reducing .NET memory consumption for dynamic tabular data
date: 2018-02-25T11:24:03+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.co.uk/2018/02/25/446-revision-v1/
permalink: /2018/02/25/446-revision-v1/
---
Semi-dynamic data schema defined by our regulatory team according to the data for rules to enforce regulations.

Imagine a table of data like the below, with potentially millions of rows, and possibly a few hundred column headers.

| AssetId | Quantity | AssetClass | Delta | TotalSharesOutstanding |
| ------- | -------- | ---------- | ----- | ---------------------- |
|  GB1234 |  3900    |  Equity    | n/a   | 10,000,000             |
|  GB2938 |  2000    |  Option    | 0.3   | n/a                    |

We were originally representing the data model essentially as a collection of objects containing a `IReadOnlyDictionary<string, object>` to represent the data in semi-dynamic schema defined by our regulatory team.

As our workloads grew in size, one of our intrepid engineering team members Matt highlighted this perhaps wasn&#8217;t the smartest thing in the world when it came to memory consumption &#8211; much of which was being used to store hash values of keys that were essentially the same across every row of table data.

Instead of storing similar keys over and over again in rows and rows of dictionary, we switched to a model whereby the rows themselves are object[] arrays, with a key indexing into these arrays defined once for the entire set of dictionaries in the collection.

**What about when you don&#8217;t know the keys up front?**

Due to the way we represent the data in the database currently, we don&#8217;t know the set of columns up front. But we do know that the first time we find an instance of a key, we can add it and move on.

**Thread safety**

If you&#8217;re sharing these dictionaries across threads, then the underlying state is potentially mutating across. When you call GetEnumerator() the collection is potentially modified by one of the other dictionary instances. We can work around this either calling .ToList() on GetEnumerator() or just using a readonly version?

&nbsp;

&nbsp;

&nbsp;

For some of our larger customers, we&#8217;re dealing with potentially millions or rows of data, which we then number crunch in-memory as a batch process.

&nbsp;

&nbsp;

There&#8217;s some state shared across all these rows,

Shared state: Dictionary<string, int>

In order to allow null values to be set, we initialise a default instance of the type we&#8217;re storing, and if we find that in the array slot, we consider that &#8217;empty&#8217;.