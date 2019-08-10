---
id: 580
title: Simple catch-all AWS budgets
date: 2018-07-12T13:35:59+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2018/07/12/546-autosave-v1/
permalink: /2018/07/12/546-autosave-v1/
---
We got caught out recently by significantly high usage of AWS CloudWatch, and realised we&#8217;d been spending $1000/month more than expected. After tracking down the cause (one of the team had turned on detailed instance monitoring) &#8211; I wanted to ensure we had a bit more of a heads up next time. We had budgets set for all the major services, but not the &#8216;small&#8217;/insigificiant ones.

The really simple (and in hindsight, obvious!) solution was just to create a &#8216;catch all&#8217; budget for all the services we didn&#8217;t have a seperate budget for:

<img class="alignnone  wp-image-578" src="https://www.jamescrowley.net/wp-content/uploads/budgets-300x254.png" alt="" width="585" height="324" /> 

When setting it up, I had to essentially just select every service available. We&#8217;d

Worked well for us so far.