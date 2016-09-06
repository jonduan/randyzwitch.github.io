---
title: RSiteCatalyst Version 1.3 Release Notes
date: 2014-02-04T09:44:19+00:00
author: Randy Zwitch
layout: post
guid: http://randyzwitch.com/?p=2507
permalink: /rsitecatalyst-version-1-3-release-notes/
tweetbackscheck:
  - 1472735864
shorturls:
  - 'a:3:{s:9:"permalink";s:30:"http://randyzwitch.com/?p=2507";s:7:"tinyurl";s:26:"http://tinyurl.com/l4n7fqc";s:4:"isgd";s:19:"http://is.gd/jDn9Z0";}'
twittercomments:
  - 'a:0:{}'
tweetcount:
  - 0
category:
  - Digital Analytics
tags:
  - Adobe Analytics
  - Omniture
  - R
  - RSiteCatalyst
---
Version 1.3 of the RSiteCatalyst package to access the Adobe Analytics API is now available on CRAN! Changes include:

  * Search via regex functionality in QueueRanked/QueueTrended functions
  * Support for Realtime API reports: Overtime and one-element Ranked report
  * Allow for variable API request timing in Queue functions
  * Fixed validate flag in JSON request to work correctly
  * Deprecated GetAdminConsoleLog (appears to be removed from the API)



### Searching via Regex functionality

RSiteCatalyst now supports the search functionality of the API, similar in nature to using the Advanced Filter/Search feature within Reports & Analytics. Here are some examples for the QueueRanked function:QueueTrended function calls work in a similar manner, returning elements broken down by time rather than a single record per element name.





### Realtime Reporting API

Accessing the <a title="Adobe Analytics realtime reports" href="https://developer.omniture.com/en_US/documentation/sitecatalyst-reporting/c-real-time#concept_AD1D9EC2BC9C4897B9DE3C99D0066B8E" target="_blank">Adobe Analytics Realtime API</a> now has limited support in RSiteCatalyst. Note that this is different than just using the currentData parameter within the Queue* functions,  as the realtime API methods provide data within a minute of that data being generated on-site. Currently, RSiteCatalyst only supports the most common types of reports: Overtime (no eVar or prop breakdown) and one-element breakdown.

Because of the extensive new functionality for the GetRealTimeConfiguration(), SaveRealTimeConfiguration() and GetRealTimeReport() functions, code examples will be provided as  separate blog post.

### Variable request timing for Queue function calls

This feature is to fix the issue of having an API request run so long that RSiteCatalyst gave up on retrieving an answer. Usually, API requests come back in a few seconds, but in selected cases a call could run so long as to exhaust the number of attempts (previously, 10 minutes). You can use the maxTries and waitTime arguments to specify how many times you'd like RSiteCatalyst to retrieve the report and the wait time between calls:

If you don't specify either of these arguments, RSiteCatalyst will default to trying every five seconds to retrieve the report, up to 120 tries.

### New Contributor: Willem Paling

I'm pleased to announce that I've got a new contributor for RSiteCatalyst, <a title="WillemPaling on Twitter" href="https://twitter.com/WillemPaling" target="_blank">Willem Paling</a>! Willem did a near-complete re-write of the underlying code to access the API, and rather than have multiple packages out in the wild, we've decided to merge our works. So look forward to better-written R code and more complete access to the Adobe Analytics API's in future releases...

### Support

If you run into any problems with RSiteCatalyst, please <a title="RSiteCatalyst GitHub issues" href="https://github.com/randyzwitch/RSiteCatalyst/issues" target="_blank">file an issue on GitHub</a> so it can be tracked properly. Note that I’m not an Adobe employee, so I can only provide so much support, as in most cases I can’t validate your settings to ensure you are set up correctly (nor do I have any inside information about how the system works <img alt=":)" src="http://i1.wp.com/randyzwitch.com/wp-includes/images/smilies/icon_smile.gif" data-recalc-dims="1" /> )

_Edit 2/20/2014: I mistakenly forgot to add the new real-time functions to the R NAMESPACE file, and as such, you won't be able to use them if you are using version 1.3. Upgrade to 1.3.1 to access the real-time functionality._