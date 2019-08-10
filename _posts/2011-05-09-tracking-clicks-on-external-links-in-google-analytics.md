---
id: 190
title: Automatically tracking outbound links in Google Analytics
date: 2011-05-09T11:53:39+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=190
permalink: /2011/05/09/tracking-clicks-on-external-links-in-google-analytics/
sfw_comment_form_password:
  - kQKkVN0Hx0Jh
categories:
  - Javascript
  - 'Marketing, SEO &amp; Analytics'
---
Google Analytics supports a nifty feature called &#8220;Events&#8221;, which is designed to allow you to track non-pageview type events. This is particularly helpful if you have an AJAX type interface on which you want to gather statistics, but another use I&#8217;ve found handy is to track clicks on external links to other sites. If you&#8217;re using the asyncronous version of the tags (if not, why not), then you should have some code that uses the `window._gaq` variable. In order to track events aside from the initial page view, you simply need to call the following each time you want to record an event:

`window._gaq.push(['_trackEvent','Event Category', 'Event Value']);`

Using your favourite javascript library (mine are jQuery and Mootools), it&#8217;s easy to hook this up to automatically fire Google event tracking for any external hyperlinks on the site. We simply look for all a tags with href attributes that begin with &#8220;http://&#8221;. Then, if the href doesn&#8217;t contain our current hostname, then we assume it&#8217;s an external link. Obviously the logic could be adjusted for your particular needs!

Here&#8217;s the mootools version:

`document.getElements('a[href^="http://"]').addEvent('click',function(link) {<br />
 &nbsp; var href = link.target.href;<br />
 &nbsp; if(href.indexOf(window.location.host) < 0) {
 &nbsp;  &nbsp; window._gaq.push(['_trackEvent','Outbound Links', href]);
 &nbsp; }
});
` 

Or for the jQuery fans amongst you, just swap the first line for:

`$('a[href^="http://"]').bind('click',function(link) {`

Now, after 24 hours or so, if you check out the "Event Tracking" page under "Content" in Google Analytics, you'll see an outbound link category listing all the external links clicked on the site (assuming Javascript is turned on, of course).

**Side note:** previous versions recommended by Google included a window.timeout before allowing the redirect to take place - in order to ensure the request to Google to record the click goes out first - as far as I can establish, this is no longer necessary.

**Side note 2:** By tracking outbound clicks this will affect your bounce rate figures. Essentially an event counts as another activity, so if a visitor lands on one page, and then clicks an external link, that will not count as a "bounce". Whether this is a fair reflection of bounces or not depends on your viewpoint - but something to bear in mind. Unfortunately there's currently no way to log an event that doesn't affect the bounce rate.