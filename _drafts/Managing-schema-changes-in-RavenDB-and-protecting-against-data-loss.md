---
id: 247
title: Managing schema changes in RavenDB, and protecting against data loss
date: 2011-09-22T14:51:16+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=247
permalink: /?p=247
categories:
  - Uncategorized
---
It is a very simple matter to do so. See below for a passing test that shows how missing values are preserved.

public class BackwardCompatabilityListner : IDocumentConversionListener  
{  
readonly ConcurrentDictionary<Type, HashSet<string>> typePropertiesCache = new ConcurrentDictionary<Type, HashSet<string>>();  
readonly ConditionalWeakTable<object, Dictionary<string, RavenJToken>> missingProps = new ConditionalWeakTable<object, Dictionary<string, RavenJToken>>();

public void EntityToDocument(object entity, RavenJObject document, RavenJObject metadata)  
{  
Dictionary<string, RavenJToken> value;  
if (missingProps.TryGetValue(entity, out value) == false)  
return;  
foreach (var kvp in value)  
{  
document[kvp.Key] = kvp.Value;  
}  
}

public void DocumentToEntity(object entity, RavenJObject document, RavenJObject metadata)  
{  
var hashSet = typePropertiesCache.GetOrAdd(entity.GetType(), type => new HashSet<string>(type.GetProperties().Select(x => x.Name)));  
foreach (var propNotOnEntity in document.Keys.Where(s => hashSet.Contains(s) == false))  
{  
missingProps.GetOrCreateValue(entity)[propNotOnEntity] = document[propNotOnEntity];  
}  
}  
}

[Fact]  
public void WillNotLoseInformation()  
{  
using(var store = NewDocumentStore())  
{  
store.DatabaseCommands.Put(&#8220;partials/1&#8221;, null,  
RavenJObject.FromObject(new {Name = &#8220;Ayende&#8221;, Email = &#8220;ayende@ayende.com&#8221;}),  
new RavenJObject());

store.RegisterListener(new BackwardCompatabilityListner());

using(var session = store.OpenSession())  
{  
var entity = session.Load<Partial>(&#8220;partials/1&#8221;);  
entity.Name = &#8220;Ayende Rahien&#8221;;  
session.SaveChanges();  
}

var doc = store.DatabaseCommands.Get(&#8220;partials/1&#8221;);  
Assert.Equal(&#8220;Ayende Rahien&#8221;, doc.DataAsJson.Value<string>(&#8220;Name&#8221;));  
Assert.Equal(&#8220;ayende@ayende.com&#8221;, doc.DataAsJson.Value<string>(&#8220;Email&#8221;));  
}  
}