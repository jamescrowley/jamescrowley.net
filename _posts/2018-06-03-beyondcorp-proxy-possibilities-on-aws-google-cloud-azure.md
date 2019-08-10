---
id: 544
title: BeyondCorp proxy possibilities on AWS, Google Cloud, Azure
date: 2018-06-03T23:36:41+00:00
author: james.crowley
layout: post
guid: https://www.jamescrowley.net/?p=544
permalink: /2018/06/03/beyondcorp-proxy-possibilities-on-aws-google-cloud-azure/
ampforwp_custom_content_editor:
  - ""
ampforwp_custom_content_editor_checkbox:
  - null
ampforwp-amp-on-off:
  - default
categories:
  - DevOps
  - 'Information Security &amp; Privacy'
---
It appears there&#8217;s now another tool in the arsenal for those looking at implementing [BeyondCorp](https://ai.google/research/pubs/pub43231) style security model, with the arrival of OIDC authentication support in AWS&#8217;s [application load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html). It adds to a growing list of possiblities, at least for HTTP-based services. Who needs VPN anyway?

The options I&#8217;m aware of now include:

  * [Bitly&#8217;s oAuth2 proxy](https://github.com/bitly/oauth2_proxy/) &#8211; a simple open source reverse proxy with OAuth support, written in Go
  * [Amazon Application load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html) &#8211; will allow you to offload authentication to a seperate IdP, and then passes claims via HTTP headers to the proxied application.
  * [Google Identity-aware proxy](https://cloud.google.com/iap/) &#8211; though this only works if the services you are securing live within the Google cloud
  * [Azure AD application proxy](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy) &#8211; Microsoft&#8217;s answer to the zero-trust model, with a lightweight proxy that sits within your internal network enabling outbound connectivity to the proxy rather than inbound.
  * [CloudFlare access](https://blog.cloudflare.com/introducing-cloudflare-access/) &#8211; hosted reverse-proxy with support for major identity providers like Azure AD and Okta
  * [ScaleFT](https://www.scaleft.com/) &#8211; commercial zero-trust platform for securing HTTP based web and SSH based server access, with a high entry cost (starts at $500/month)
  * [Pritunl Zero](https://zero.pritunl.com/) &#8211; a freemium SaaS service offering HTTP and SSH based proxying.
  * [DuoBeyond](https://duo.com/pricing/duo-beyond)

Any others I&#8217;m missing? Would love to hear of folks experiences of these.