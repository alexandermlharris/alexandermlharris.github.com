---
layout: post
category : development
tags : [microsoft, mvc.net, c#]
---
{% include JB/setup %}

In a recent MVC.Net web site we were working on at work, we had a problem rendering the fonts we were using on the web servers. The fonts worked fine on our local machines during development, but just rendered as squares when deployed to the DEV / Live web server.

The project was based on a Twitter Bootstrap template (Unicorn Admin) which included a set of fonts `fontawesome-webfont.eot, .svg, .ttf & .woff`. The problem was in Chrome and IE and was in two parts.

### Deploying the fonts ###
First of all, you need to make sure Visual Studio is deploying the fonts when you publish. Ensure all are included in the project, then right click on each and set 

	Build Action: 			Content
	Copy to Output Directory: 	Copy if newer

This will ensure that your fonts make it to the web server.

### Setting the MIME types ###
We found this didn't entirely resolve the issue and there were still some rendering issues caused by the resources being interpreted incorrectly. We solved this by setting the MIME Type for these files in IIS.

First, find the MIME types editor:

	Right Click on IIS WebSite --> Properties --> HTTP Headers Tab --> MIME Types button

Then set custom mime types for the font files as follows by clicking the `New...` button.

	Extension: .ttf		MIME type: application/x-font-ttf
	Extension: .woff	MIME type: application/font-woff

This solved our problems and got our pretty fonts rendering properly again.
