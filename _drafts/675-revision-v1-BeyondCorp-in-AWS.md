---
id: 677
title: BeyondCorp in AWS
date: 2018-07-27T21:34:28+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2018/07/27/675-revision-v1/
permalink: /2018/07/27/675-revision-v1/
---
CloudFlare released Access &#8211; interesting, but requires us to trust cloudflare with our traffic &#8211; not an option.

Could have self-hosted.

AWS announcement auth support.

the other piece of the puzzle is an identity provider. at it&#8217;s simplest, you can choose any standard one (Okta, OneLogin etc). However, you probably want to use support for device trust with conditional access.