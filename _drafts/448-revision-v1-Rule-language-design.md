---
id: 451
title: Rule language design
date: 2018-02-22T23:23:14+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.co.uk/2018/02/22/448-revision-v1/
permalink: /2018/02/22/448-revision-v1/
---
AST

At FundApps our regulatory team write rules representing regulation in a domain specific language that is executed in a rules based engine, written in C#. This gives us great flexibility keeping our service up to date with the latest regulatory changes without requiring involvement from the engineering team.

Visitor

Analysis