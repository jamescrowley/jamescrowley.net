---
id: 241
title: NServiceBus request/reponse with AsyncController in ASP.NET MVC
date: 2011-09-15T11:58:25+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=241
permalink: /?p=241
categories:
  - Uncategorized
---
The majority of the time (and benefit) from using nServiceBus is using it&#8217;s fire & forget publish/subscribe model. Sometimes however, it might be appropriate to fire off a command and keep the client waiting for the message to be processed &#8211; potentially handling differently if a response doesn&#8217;t come back in a particular timeframe.

ASP.NET MVC has a notion of AsyncController that allows asynchronous requests so you are not using up the available threads in the thread pool while waiting for a response. It&#8217;s relatively easy to get 

[AsyncTimeout(20000)]  
public void IndexAsync(string param)  
{  
AsyncManager.OutstandingOperations.Increment();  
_bus.Send(new MyCommand(param))  
.Register(ConvertCodeCallback, this);  
}

In the handler:

public void Handle(MyCommand message)  
{  
// do stuff  
Bus.Reply(new MyCommandCompleted() { Result = &#8220;xyz });  
}

Then in the callback

private void ConvertCodeCallback(IAsyncResult asyncResult)  
{  
var result = asyncResult.AsyncState as CompletionResult;  
AsyncManager.Parameters[&#8220;response&#8221;] = result.Messages[0] as MyCommandCompleted;  
AsyncManager.OutstandingOperations.Decrement();  
}

The AsyncController pumbling will then do:

public ActionResult IndexCompleted(MyCommandCompletedresponse)  
{

}

Then for handling timeouts:

protected override void OnException(ExceptionContext filterContext)  
{  
if (filterContext.Exception is TimeoutException)  
{  
filterContext.Result = RedirectToAction(&#8220;Timeout&#8221;);  
filterContext.ExceptionHandled = true;  
}  
base.OnException(filterContext);  
}