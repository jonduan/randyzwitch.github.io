---
title: Adobe Analytics Implementation Documentation in 60 Seconds
date: 2013-12-09T09:46:30+00:00
author: Randy Zwitch
layout: post
permalink: /adobe-analytics-implementation-documentation/
category: Analytics
description: Generating documentation for an Adobe Analytics implementation doesn't need to be an ordeal. Using the RSiteCatalyst package for R takes less than a minute.
tags:
  - Adobe Analytics
  - Omniture
  - R
  - RSiteCatalyst
---
When I was working as a digital analytics consultant, no question quite had the ability to cause belly laughs AND angst as, "Can you send me an updated copy of your implementation documentation?" I saw companies that were spending six-or-seven-figures annually on their analytics infrastructure, multi-millions in salary for employees and yet the only way to understand what data they were collecting was to inspect their JavaScript code.

Luckily for Adobe Analytics customers, the API provides a means of generating the framework for a properly-documented implementation. Here's how to do it using <a title="RSiteCatalyst CRAN" href="http://cran.r-project.org/web/packages/RSiteCatalyst/index.html" target="_blank">RSiteCatalyst</a>.

## Generating Adobe Analytics documentation file

The code below outlines the commands needed to generate an Excel file (<a title="Example Excel file Adobe Analytics Documentation" href="http://randyzwitch.com/wp-content/uploads/2013/12/adobe_analytics_implementation_doc.xlsx" target="_blank">see example</a>) with six tabs containing the basic structure of an Adobe Analytics. This report contains all of the report suites you have access to, the elements that reports can be broken down by, traffic variables (props), conversion variables (eVars) and segments available for reporting.

Additionally, within each tab metadata is provided that contains the various settings for variables, so you'll be able to document the expiration settings for eVars, participation, list variables, segment types and so on. 

{% highlight r linenos %}
library("RSiteCatalyst")
library("WriteXLS")

#Validate that underlying Perl modules for WriteXLS are installed correctly
#Will return "Perl found. All required Perl modules were found" if installed correctly
testPerl()

#### 1. Pull data for all report suites to create one comprehensive report ####

#Authenticate with Adobe Analytics API
SCAuth("user:company", "sharedsecret")

#Get Report Suites
report_suites <- GetReportSuites()

#Get Available Elements
elements <- GetElements(report_suites$rsid)

#Get eVars
evars <- GetEvars(report_suites$rsid)

#Get Segments
segments <- GetSegments(report_suites$rsid)

#Get Success Events
events <- GetSuccessEvents(report_suites$rsid)

#Get Traffic Vars
props <- GetProps(report_suites$rsid)

#### 2. Generate a single Excel file

#Create list of report suite objects, written as strings
objlist <- c("report_suites", "elements", "evars", "segments", "events", "props")

#Write out Excel file with auto-width columns, a bolded header row and filters turned on
WriteXLS(objlist, "/Users/randyzwitch/Desktop/adobe_analytics_implementation_doc.xlsx",
         AdjWidth = TRUE, BoldHeaderRow = TRUE, AutoFilter = TRUE)
{% endhighlight %}

The only "gotchas" to keep in mind when using the script above is that the user running this will only receive data for report suites they have access to (which is determined by Admin panel setting within Adobe Analytics) and that you need to have the <a title="WriteXLS" href="http://cran.r-project.org/web/packages/WriteXLS/index.html" target="_blank">WriteXLS</a> package installed to write to Excel. The WriteXLS package uses Perl as the underlying code, so you'll need to validate that the package is installed correctly, which is done using the `testPerl()` function in the package.

## This is pretty bare-bones, no?

After you run this code, you'll have an Excel file that has all of the underlying characteristics of your Adobe Analytics implementation. It's important to realize that this is only the _starting_ point; a great set of documentation will contain other pieces of information such as where/when the value is set (on entry, every page, when certain events occur, etc.), a layman's explanation about what the data element means and other _business information_ so your stakeholders can be confident they are using the data correctly. Additionally, you might consider creating a single Excel file for every report suite in your implementation. It's trivial to modify the code above to subset each data frame used above for a single value of rsid, then write to separate Excel files. Regardless of how your structure your documentation, DOCUMENT YOUR IMPLEMENTATION! The employees that come after you (and your future self!) will thank you.

_EDIT 2/4/2016: Thanks to reader <a href="https://twitter.com/CSitty" target="_blank">@CSitty</a> for pointing out the R code became a little stale. The documentation generating code should now work again for RSiteCatalyst versions >= 1.4 and WriteXLS >= 4.0 (basically, any current version as of the time of this update)._
