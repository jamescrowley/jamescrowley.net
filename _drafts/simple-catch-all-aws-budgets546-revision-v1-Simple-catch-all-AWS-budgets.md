---
id: 582
title: Simple catch-all AWS budgets
date: 2018-07-12T13:37:14+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2018/07/12/546-revision-v1/
permalink: /2018/07/12/546-revision-v1/
---
We got caught out recently by significantly high usage of AWS CloudWatch, and realised we&#8217;d been spending $1000/month more than expected. After tracking down the cause (one of the team had turned on detailed instance monitoring) &#8211; I wanted to ensure we had a bit more of a heads up next time. We had budgets set for all the major services, but not the &#8216;small&#8217;/insigificiant ones.

The really simple (and in hindsight, obvious!) solution was just to create a &#8216;catch all&#8217; budget for all the services we didn&#8217;t have a seperate budget for, even if we&#8217;re not currently using them:

<img class="alignnone wp-image-578 size-large" src="https://www.jamescrowley.net/wp-content/uploads/budgets-1024x866.png" alt="" width="840" height="710" srcset="https://www.jamescrowley.net/wp-content/uploads/budgets-1024x866.png 1024w, https://www.jamescrowley.net/wp-content/uploads/budgets-300x254.png 300w, https://www.jamescrowley.net/wp-content/uploads/budgets-768x650.png 768w, https://www.jamescrowley.net/wp-content/uploads/budgets-1200x1015.png 1200w, https://www.jamescrowley.net/wp-content/uploads/budgets.png 1232w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

I&#8217;d probably move this to terraform next, but this works well for us so far.