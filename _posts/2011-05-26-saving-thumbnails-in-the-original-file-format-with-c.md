---
id: 207
title: 'Saving thumbnails in the original file format with C#'
date: 2011-05-26T12:54:18+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=207
permalink: /2011/05/26/saving-thumbnails-in-the-original-file-format-with-c/
sfw_comment_form_password:
  - WGb6edIEfCyk
categories:
  - 'C#'
  - Web Development
---
I tripped up on a strange quirk working with the Image and ImageFormat classes recently. The intention was simple &#8211; load an Image object from an existing graphic, generate a thumbnail, and save it out in the original format. The Image class in .NET includes a handy &#8220;RawFormat&#8221; property indicating the correct format to save out in. So far, so easy. Except the object that RawFormat was returning didn&#8217;t seem to match any supported ImageFormat, and the Guid was one character out. For example, when loading a JPEG, you got:

b96b3caa-0728-11d3-9d7b-0000f81ef32e

when the Guid for ImageFormat.Jpeg.Guid was in fact

b96b3cae-0728-11d3-9d7b-0000f81ef32e

It turns out that the &#8220;RawFormat&#8221; seems to change to an internal format the moment you start modifying the original image. So the simple trick is to save the value of the RawFormat property first, do your modifications, and then save out the image using the original RawFormat value.