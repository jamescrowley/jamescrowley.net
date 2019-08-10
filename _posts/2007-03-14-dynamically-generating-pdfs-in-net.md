---
id: 75
title: Dynamically Generating PDFs in .NET
date: 2007-03-14T13:12:00+00:00
author: james.crowley
layout: post
guid: /james_crowley/archive/2007/03/14/dynamically-generating-pdfs-in-net.aspx
permalink: /2007/03/14/dynamically-generating-pdfs-in-net/
sfw_comment_form_password:
  - e1gKtfY78c7X
categories:
  - Uncategorized
---
It's perfectly possible to generate a PDF from scratch, using a library such as [iTextSharp](http://sourceforge.net/projects/itextsharp/), a port of a free Java PDF library. However, it can be hard work defining all the code you need to generate the layout you're after, and impossible for someone to tweak the layout without going back to the developer. 

One alternative, using the same free library, is instead to design a PDF document in a WYSIWYG environment such as Adobe Designer, and define some dynamic fields within the document. Your code can load this PDF form,&nbsp;set the value of each of the fields, and output a flat PDF. This would enable you to, for instance, have an invoice PDF template defined,set a bunch of fields within the PDF,and output as a flat PDF document &#8211; without needing a load of code generating the PDF from scratch. 

The code itself is very straightforward. We're going to send the resulting output to the ASP.NET response stream, so we need to set the content-type and also a content-disposition header so that the result appears as a file download. 

`Response.ContentType = "application/pdf";<br />Response.AddHeader("Content-disposition", "attachment; filename=tokens.pdf");`

Note that we could just as easily be using a file stream, and would therefore replace the above with opening a stream to the appropriate file path. With that out of the way, we use a `PdfReader` object to load our existing PDF from the local file system; in this case, from `~/assets/form.pdf`. 

`PdfReader pdfReader = new PdfReader(Request.MapPath("~/assets/form.pdf"));`

Next we create a `PdfStamper` object, which will allow us to modify the form fields defined in the PDF and save the result. We pass it the `PdfReader` object representing the PDF file, and a stream we want the result sent to. Finally, we set the `FormFlattening` flag so that we get a flat PDF rather than allowing the fields to remain editable. 

`PdfStamper pdfStamper = new PdfStamper(pdfReader, Response.OutputStream);<br />pdfStamper.FormFlattening = true; // generate a flat PDF` 

We can then set the dynamic fields defined within the PDF form, and close our `pdfStamper` object. 

`AcroFields pdfForm = pdfStamper.AcroFields;<br />pdfForm.SetField("InvoiceRef", "00000");<br />pdfForm.SetField("DeliveryAddress", "Oxford Street, London");<br />pdfForm.SetField("Email", "anon@anywhere.com");<br />pdfStamper.Close();`

Closing the `pdfStamper` object results in the PDF being written to the stream we passed in the constructor, and we're all done. Simple!