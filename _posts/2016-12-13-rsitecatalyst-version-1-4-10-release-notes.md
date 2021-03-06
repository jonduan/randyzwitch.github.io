---
title: RSiteCatalyst Version 1.4.10 Release Notes
date: '2016-12-13'
author: Randy Zwitch
layout: post
permalink: /rsitecatalyst-version-1-4-10-release-notes/
description: Version 1.4.10 of RSiteCatalyst adds a QueueDatawarehouse method, as well as some bug fixes and administrative API calls
category: Analytics
tags:
- Adobe Analytics
- Omniture
- R
- RSiteCatalyst
---

Version 1.4.10 of RSiteCatalyst brings a handful of new `Get*` methods, `QueueDatawarehouse` and a couple of bugs fixes/low-level code improvements.

## QueueDatawarehouse

The most useful user-facing change IMO is the addition of the `QueueDatawarehouse` method, which allows for submitting (and _sometimes_ retrieving) Data Warehouse requests via R. This should be a huge timesaver for those of you using Data Warehouse as a substitute for the Adobe Analytics raw data feed (my employer alone pulls hundreds of Data Warehouse feeds per day).

In the coming days, I'll write a blog post in more detail about how to use this method effectively to query Data Warehouse, but in the meantime, here's a sample function call:

{% highlight r linenos %}
#API Credentials
SCAuth(Sys.getenv("USER", ""), Sys.getenv("SECRET", ""))
#FTP Credentials
FTP <- Sys.getenv("FTP", "")
FTPUSER <- Sys.getenv("FTPUSER", "")
FTPPW <- Sys.getenv("FTPPW", "")

#Write QueueWarehouse to FTP
report.id <- QueueDataWarehouse("report-suite",
                                "2016-11-01",
                                "2016-11-07",
                                c("visits", "pageviews"),
                                c("page"),
                                enqueueOnly=TRUE,
                                ftp = list(host = FTP,
                                           port = "21",
                                           directory = "/DWtest/",
                                           username = FTPUSER,
                                           password = FTPPW,
                                           filename = "myreport.csv")
)
{% endhighlight %}

## ViewProcessingRules

`ViewProcessingRules` has also been added on an experimental basis, as this API method is not documented (as of the writing of this post), so Adobe may choose to modify or remove this in a future release. As the method name indicates, this new feature allows for the viewing of the processing rules for a list of report suites, including the behaviors that define the rules. There is currently no (public) method for _setting_ processing rules via the API.

As processing rules are a super-user functionality, I would love it if someone in the community could verify that this method works for a large number of report suites/rules.

## Get* method additions

Three Get* methods were added in this release: `GetVirtualReportSuiteSettings`, `GetReportSuiteGroups`, `GetTimeStampEnabled`, each corresponding to settings able to be viewed within the Adobe Analytics admin panel.

## Bug fixes

- GetRealTimeSettings: fixed bug where a passing a list of report suites lead to a parsing error

- QueueSummary: Redefined method arguments to make `date` an optional parameter, allowing for more elegant use of `date.to` and `date.from` parameters.

## Package-level improvements

At long last, I've added integration testing via the AppVeyor testing service. For the longest time, I've had a very nonchalant attitude towards Windows (since I don't use it), but given that RSiteCatalyst is enterprise software and so many businesses use Windows, I figured it was time.

Luckily, there were none of the tests in the test suite throws any errors specifically due to Windows, so effectively this change is just defensive programming against that class of error in the future.

## Community Contributions

As in the past several releases, there have been contributions from the community keeping RSiteCatalyst moving forward! Special thanks to Diego Villuendas Pellicero for writing the `QueueDataWarehouse` functionality and Johann de Boer for highlighting that `QueueSummary` could be improved.

I encourage all users of the software to continue reporting bugs via GitHub issues, and especially if you can provide a working code example. Even better, a fix via pull request will ensure that your bug will be addressed in a timely manner and for the benefit to others in the community.
