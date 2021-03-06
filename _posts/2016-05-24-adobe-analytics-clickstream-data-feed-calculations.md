---
title: 'Adobe Analytics Clickstream Data Feed: Calculations and Outlier Analysis'
date: 2016-05-24T11:11:20+00:00
author: Randy Zwitch
layout: post
permalink: /adobe-analytics-clickstream-data-feed-calculations/
category: Analytics
description: Using a technique called local outlier factors, we can the Adobe Analytics data feed to identify IP addresses that show unusual site behavior.
custom_js:
  - 2016-05-24-lofgraph.js
tags:
  - Adobe Analytics
  - Data Visualization
  - Omniture
  - R
  - SQL
---
In a previous post, I outlined how to load [daily Adobe Analytics Clickstream data feeds](http://randyzwitch.com/adobe-analytics-clickstream-data-feed-relational-database/) into a PostgreSQL database. While this isn't a long-term scalable solution for large e-commerce companies doing millions of page views per day, for exploratory analysis a relational database structure can work well until a more robust solution is put into place (such as Hadoop/Spark).

## Data Validation <groan>

Before digging too deeply into the data, we should validate that data from the data feed in our database ([custom database view code](https://gist.github.com/randyzwitch/7a9c48e7132e6ed9dfb0d02ec906961c)) matches what we observe from other sources (mainly, the Adobe Analytics interface and/or [RSiteCatalyst](http://randyzwitch.com/tag/rsitecatalyst/)). Given the Adobe Analytics data feed represents an export of the underlying data, and Adobe provides the formulas in the [data feed documentation](https://marketing.adobe.com/resources/help/en_US/sc/clickstream/datafeeds_calculate.html), _in theory_ you should be able to replicate the numbers exactly:

{% highlight R linenos %}
# "Source 1": Pull data from the API using RSiteCatalyst
library("RSiteCatalyst")
SCAuth(Sys.getenv("USER", ""), Sys.getenv("SECRET", ""))
overtime <- QueueOvertime("zwitchdev",
                           date.from = "2016-04-01",
                           date.to = "2016-05-17",
                           metrics = c("pageviews", "visits", "visitors"),
                           date.granularity = "day")

# "Source 2": Pull data from Postgres database
library(RPostgreSQL)

# Connect to database
conn <- dbConnect(dbDriver("PostgreSQL"),
                 user="postgres",
                 password="",
                 host="localhost",
                 port=5432,
                 dbname="adobe")

dbdata <- dbGetQuery(conn,
                     "select
                     date(date_time) as date_localtime,
                     sum(CASE WHEN post_page_event = '0' THEN 1 END) as pageviews,
                     count(distinct ARRAY_TO_STRING(ARRAY[post_visid_high::text, post_visid_low::text, visit_num::text], '')) as visits,
                     count(distinct ARRAY_TO_STRING(ARRAY[post_visid_high::text, post_visid_low::text], '')) as visitors
                     from usefuldata
                     where date_time between '2016-04-01' and '2016-05-18' and exclude_hit = '0'
                     group by 1
                     order by 1;")

# Compare data sources
> diff_pv = table(overtime$pageviews - dbdata$pageviews)
> diff_pv

0
47

> diff_visits = table(overtime$visits - dbdata$visits)
> diff_visits

0
47

> diff_visitors = table(overtime$visitors - dbdata$visitors)
> diff_visitors

0
47
{% endhighlight %}

The code snippet above shows the validation, and sure enough, the "two different sources" show the same exact values (i.e. differences are 0), so everything has been loaded properly into the PostgreSQL database.

## Finding Anomalies For Creating Bot Rules

With the data validated, we can now start digging deeper into the data. As an example, although I have [bot filtering](https://marketing.adobe.com/resources/help/en_US/reference/bot_rules.html) enabled, this only handles bots on the [IAB bot list](http://www.iab.com/guidelines/iab-abc-international-spiders-bots-list/) but not necessarily people trying to scrape my site (or worse).

To create a [custom bot rule in Adobe Analytics](https://marketing.adobe.com/resources/help/en_US/reference/t_create_bot_rules.html), you can use IP address(es) and/or User-Agent string. However, as part of data exploration we are not limited to just these features (assuming, of course, that you can map your feature set back to an IP/User-Agent combo). To identify outlier behavior, I'm going to use a technique called '[local outlier factors](http://www.dbs.ifi.lmu.de/Publikationen/Papers/LOF.pdf)' using the [Rlof](https://cran.r-project.org/web/packages/Rlof/index.html) package in R with the following data features:

  * Distinct Days Visited
  * Total Pageviews
  * Total Visits
  * Distinct Pages Viewed
  * Pageviews Per Visit
  * Average Views Per Page

These aren't the only features I could've used, but it should be pretty easy to view bot/scraper traffic using these metrics. Here's the code:

{% highlight R linenos %}
# Local outlier factor calculation
library(RPostgreSQL)
library(Rlof)

conn <- dbConnect(dbDriver("PostgreSQL"),
                  user="postgres",
                  password="",
                  host="localhost",
                  port=5432,
                  dbname="adobe")

metrics_lof <- dbGetQuery(conn,
                          "select
                          ip,
                          distinct_days_visited,
                          pageviews,
                          visits,
                          distinct_pages_viewed,
                          pageviews/visits::double precision as pv_per_visit,
                          pageviews/distinct_pages_viewed::double precision as avg_views_per_page
                          from
                          (
                          select
                          ip,
                          sum(CASE WHEN post_page_event = '0' THEN 1 END) as pageviews,
                          count(distinct ARRAY_TO_STRING(ARRAY[post_visid_high::text, post_visid_low::text, visit_num::text, visit_start_time_gmt::text], '')) as visits,
                          count(distinct post_pagename) as distinct_pages_viewed,
                          count(distinct date(date_time)) as distinct_days_visited
                          from usefuldata
                          where exclude_hit = '0'
                          group by 1
                          ) a
                          where visits > 1 and pageviews > 1;")


# The higher the value of k, the more likely lof will be calculated...
# ...but more generic the clusters
# NaN/Inf occurs with points on top of one another/div by zero, which is likely...
# ...with web data when most visitors have 1-2 sessions
df_lof<-lof(metrics_lof[, 2:7],k = 20)

# Append results, get top 500 worst scoring IP addresses
results <- cbind(metrics_lof, df_lof)[order(-df_lof),]
worst500 <- head(subset(results, !is.infinite(df_lof)), 500)
{% endhighlight %}

A local outlier factor greater than 1 is classified as a potential outlier. Here's a visual of the lof scores for the top 500 _worst_ scoring IP addresses [(vegalite R graph code)](https://gist.github.com/randyzwitch/178d72e01e30943f6af82c48a47c4478):

<div id="vis"></div>

We can see from the graph that there are at least 500 IP addresses that are potential outliers (since the line doesn't go below a lof value of 1). These points are now a good starting place to go back to our overall table and inspect the entire datafeed records by IP address.

## But what about business value?

The example above just scratches the surface on what's possible when you have access to the raw data from Adobe Analytics. It's possible to do these calculations on my laptop using R because I only have a few hundred-thousand records and IP addresses. But this kind of ops work is pretty low-value, since unless you are trying to detect system hacking, trying to find hidden scrapers/spiders in your data to filter out just modifies the denominator of your KPIs it doesn't lead to real money per se.

In the last post of this series, I'll cover how to work with the datafeed using Spark, and provide an example of using [Spark MLLib](http://spark.apache.org/docs/latest/mllib-guide.html) to increase site engagement.
