---
id: 238
title: NServiceBus audit queues
date: 2011-09-07T10:54:08+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=238
permalink: /2011/09/07/nservicebus-audit-queues/
sfw_comment_form_password:
  - 5cNyCNKHXHQM
categories:
  - Uncategorized
---
Being new to the world of NServiceBus, I just thought I&#8217;d share a few gotcha&#8217;s as I experience them.

When everything&#8217;s up and running there&#8217;s no easy way to see what&#8217;s going on as messages appear and disappear from the normal message queue very quickly. You can use an audit queue to log all messages appearing on a queue. To do this, in your app config you simply need to use the ForwardReceivedMessagesTo attribute, like so:

`<UnicastBusConfig ForwardReceivedMessagesTo="MyAuditQueue@MachineName"><br />
....<br />
</UnicastBusConfig>`

NServiceBus won&#8217;t automatically create an audit queue, so when you do so manually.

You can do this in code using:

`NServiceBus.Utils.MsmqUtilities.CreateQueueIfNecessary(QueueName)`

Alternatively, you can create it using the admin interface, but you need to ensure it has the same settings and permissions as the NServiceBus queues. Notably, that SYSTEM has permissions on the queue, and that it is transactional (if your queue is) &#8211; otherwise your audit queue will remain empty!