---
id: 520
title: Technical work
date: 2018-08-03T21:58:43+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2018/06/03/516-autosave-v1/
permalink: /2018/08/03/516-autosave-v1/
---
I&#8217;ve worked on all sorts of technical challenges and projects over the years &#8211; some my own, with others, and for others. Here&#8217;s a few highlights.

### The fintech challenges&#8230; (WIP)

#### Domain specific languages   <em style="font-size: 70%; font-weight: normal;">FundApps &#8211;</em>

Powerful DSL to express regulation.

#### Security   <em style="font-size: 70%; font-weight: normal;">FundApps &#8211;</em>

&nbsp;

### The start-up challenges&#8230; (WIP)

#### In-line text ad placement   <em style="font-size: 70%; font-weight: normal;">TechClicks &#8211; 2010</em>

Placing a standard ad-tag on the page, we&#8217;d intelligently identify article content and place our AdWords style text ads to naturally appear within the the flow of the core content. Scaling to XX M impressions.

#### Automated SEO & Social Media   <em style="font-size: 70%; font-weight: normal;">TechEye &#8211; 2010</em>

&nbsp;

#### Social graph crawling & authority detection   <em style="font-size: 70%; font-weight: normal;">BlipNetwork &#8211; 2009</em>

Identifying though leaders by combining social profiles using Google&#8217;s Social Graph API, extracting blog and article content and NLP to identify appropriate categories.

#### Automated code language conversion   <em style="font-size: 70%; font-weight: normal;">Developer Fusion &#8211; 2009</em>

&nbsp;

### The agency projects&#8230;

#### AJAX infinite panning   <em style="font-size: 70%; font-weight: normal;">BigWhiteWall @ Anorak digital &#8211; 2007</em>

Working with the BigWhiteWall founders from initial concept for their [digital community for mental health,](http://www.bigwhitewall.com) they envisagioned a virtual wall of artwork from their users. I devised an AJAX based infinitely pannable, and zoomable &#8216;wall&#8217; with &#8216;bricks&#8217; of user generated artwork loading on demand, inspired by Google Maps.

#### Accessible font embedding  <em style="font-size: 70%; font-weight: normal;">Anorak digital &#8211; 2006</em>

Our designers loved using non-web fonts, and our team hated cutting header images out of photoshop. I devised a system that would replace headers with the desired fonts whilst preserving accessibility. Simply by adding a JavaScript tag to the page, the required CSS and images would be automatically generated, embedded and cached.

#### Continuous integration, automated deployments, SOA  <em style="font-size: 70%; font-weight: normal;">Anorak digital &#8211; 2006</em>

I introduced highly available infrastructure at Rackspace to enable us to host worldwide campaigns for customers. CruiseControl and Web Deploy were used for continuous integration and automated deployments, practices that were rare in the digital agency space at the time. I architected and built out a set of core set of services for features repeatedly needed for customer campaigns &#8211; full text search with Lucene, competition mechanics, user registration, email delivery.

#### Continuous integration, automated deployments, SOA  <em style="font-size: 70%; font-weight: normal;">REDBURN &#8211; 2006</em>

&nbsp;

### The pre-graduation years&#8230;

#### &#8216;CAPTIVE PORTAL&#8217; for NETWORK ACCESS &#8211; 200_5_

Working with the IT office at college and a fellow student, we created a &#8216;captive portal&#8217; to welcome and register students and conference attendees on the college network when they first connected a device, validating their identity with their student ID and mac address, and integrating with firewall configuration to then whitelist those devices.

#### Content management &#8211; vCommune &#8211; 200_5_

As my [third year project](https://www.jamescrowley.net/wp-content/uploads/Content-Management-System-v0.3.pdf) at university, I wrote about the challenges building and running a large scale CMS, which I&#8217;d been doing since 1999 on Developer Fusion. This included storing hierarchical data in a relational database, finite state machines to model content management workflow, hashing passwords and SQL injection risks, n-tier architecture, rich content editing, CAPTCHA&#8217;s to block spam, desktop applications consuming the same public APIs, and tackling concurrency issues with multiple authors editing the same content.

#### e-Commerce platform &#8211; LondonPass &#8211; 2002 <em style="font-size: 50%;">(@ mission communications)</em>

I built a new e-commerce platform for [LondonPass](http://www.londonpass.com) that at the time processed several £M a year in transactions, integrating with WorldPay and SecPay. The platform included sales analysis for both boardroom to front-office, and I supported its implementation with training & troubleshooting.

#### Commercial web crawling library &#8211; WebZinc &#8211; 2002

I created the C# version of [WebZinc](http://www.webzinc.com) for WhiteCliff, a commercial component that allows developers to extract content from web sites, parse content reliably and automate form filling tasks. Tackled some fun browser integration, intelligent text parsing, documentation packaging (remember CHM files?!), ilmerge-ing the dependencies to create a simpler package, packaging with MSI, and learning the challenges of supporting and versioning an API that other engineers depended on.

#### Freeware code editor &#8211; Developers Pad &#8211; 1999

One of my first significant personal projects, I created a free customizable development editor written in Visual Basic, which was reviewed in leading magazine, PC Pro. I was pushing the limits of what was possible with VB at the time &#8211; using low-level Windows API calls to build UI features like syntax highlighting, dockable windows (there wasn&#8217;t a standard way of doing this back VB days&#8230;), and extending the standard &#8216;common dialog&#8217; windows.