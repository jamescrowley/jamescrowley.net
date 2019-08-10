---
id: 97
title: 'Posting to Facebook Page using C# SDK from offline app'
date: 2011-02-03T11:59:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2011/02/03/posting-to-facebook-page-using-c-sdk-from-quot-offline-quot-app.aspx
permalink: /2011/02/03/posting-to-facebook-page-using-c-sdk-from-offline-app/
sfw_comment_form_password:
  - br1xEQRTpad3
categories:
  - 'C#'
  - 'Marketing, SEO &amp; Analytics'
  - Web Development
---
If you want to post to a facebook page using the [Facebook Graph API](http://developers.facebook.com/docs/reference/api/page/) and the [Facebook C# SDK](http://facebooksdk.codeplex.com/), from an &#8220;offline&#8221; app, there&#8217;s a few steps you should be aware of.

First, you need to get an access token that your windows service or app can permanently use. You can get this by visiting the following url (all on one line), replacing [ApiKey] with your applications Facebook API key.

<pre>http://www.facebook.com/login.php?api_key=[ApiKey]&#038;connect_display=popup&#038;v=1.0
&#038;next=http://www.facebook.com/connect/login_success.html&#038;cancel_url=http://www.facebook.com/connect/login_failure.html
&#038;fbconnect=true&#038;return_session=true&#038;req_perms=publish_stream,offline_access,manage_pages&#038;return_session=1
&#038;sdk=joey&#038;session_version=3
</pre>

In the parameters of the URL you get redirected to, this will give you an access key. Note however, that this only gives you an access key to post to your own profile page. Next, you need to get a separate access key to post to the specific page you want to access. To do this, go to 

<pre>https://graph.facebook.com/[YourUserId]/accounts?access_token=[AccessTokenFromAbove]
</pre>

You can find your user id in the URL when you click on your profile image. On this page, you will then see a list of page IDs and corresponding access tokens for each facebook page. Using the appropriate pair,you can then use code like this:

<pre>var app = new Facebook.FacebookApp(_accessToken);
var parameters = new Dictionary&lt;string,object>
{
    { "message",  promotionInfo.TagLine },
    { "name" ,  promotionInfo.Title },
    { "description" ,  promotionInfo.Description },
    { "picture", promotionInfo.ImageUrl.ToString() },
    { "caption" ,  promotionInfo.TargetUrl.Host },
    { "link" ,  promotionInfo.TargetUrl.ToString() },
    { "type" , "link" },
};
app.Post(_targetId + "/feed", parameters);
</pre>

And you&#8217;re done!