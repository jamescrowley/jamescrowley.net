---
id: 675
title: BeyondCorp in AWS
date: 2018-07-27T21:34:52+00:00
author: james.crowley
layout: post
guid: https://www.jamescrowley.net/?p=675
permalink: /?p=675
ampforwp_custom_content_editor:
  - ""
ampforwp_custom_content_editor_checkbox:
  - null
ampforwp-amp-on-off:
  - default
categories:
  - Uncategorized
---
CloudFlare released Access &#8211; interesting, but requires us to trust cloudflare with our traffic &#8211; not an option.

Could have self-hosted.

AWS announcement auth support.

the other piece of the puzzle is an identity provider. at it&#8217;s simplest, you can choose any standard one (Okta, OneLogin etc). However, you probably want to use support for device trust with conditional access.