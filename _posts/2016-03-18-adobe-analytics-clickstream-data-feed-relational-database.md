---
title: 'Adobe Analytics Clickstream Data Feed: Loading To Relational Database'
date: 2016-03-18T14:42:23+00:00
author: Randy Zwitch
layout: post
permalink: /adobe-analytics-clickstream-data-feed-relational-database/
category: Analytics
description: When dealing with the Adobe Analytics Clickstream Data Feed, one approach is to load the files into a relational database. Here's how using R.
tags:
  - Adobe Analytics
  - Omniture
  - R
  - SQL
---
In my previous post about the [Adobe Analytics Clickstream Data Feed](http://randyzwitch.com/adobe-analytics-clickstream-raw-data-feed/), I showed how it was possible to take a single day worth of data and build a dataframe in R. However, most likely your analysis will require using multiple days/weeks/months of data, and given the size and complexity of the feed, loading the files into a relational database makes a lot of sense.

Although there may be database-specific "fast-load" tools more appropriate for this application, this blog post will show how to handle this process using only R and [PostgresSQL](http://www.postgresql.org/download/).

## File Organization

Before getting into the loading of the data into PostgreSQL, I like to sort my files by type into separate directories (remember from the [previous post](http://randyzwitch.com/adobe-analytics-clickstream-raw-data-feed/), you'll receive three files per day). R makes OS-level operations simple enough:

{% highlight R linenos %}
#### 1. Setting directory to FTP folder where files incoming from Adobe
## Has ~2000 files in it from 2 years of data
setwd("~/Downloads/datafeed/")

#### 2. Sort files into three separate folders
## Manifests - plain text files
if(!dir.exists("manifest")){
  dir.create("manifest")
  lapply(list.files(pattern = "*.txt"), function(x) file.rename(x, paste("manifest", x, sep = "/")))
}

## Server calls tsv.gz
if(!dir.exists("servercalls")){
  dir.create("servercalls")
  lapply(list.files(pattern = "*.tsv.gz"), function(x) file.rename(x, paste("servercalls", x, sep = "/")))
}

## Lookup files .tar.gz
if(!dir.exists("lookup")){
  dir.create("lookup")
  lapply(list.files(pattern = "*.tar.gz"), function(x) file.rename(x, paste("lookup", x, sep = "/")))
}
{% endhighlight %}

Were there more file types, I could've abstracted this into a function instead of copying the code three times, but the idea is the same: Check to see if the directory exists, if it doesn't then create it and move the files into the directory.

## Connecting and Loading Data to PostgreSQL from R

Once we have our files organized, we can begin the process of loading the files into PostgreSQL using the [RPostgreSQL](https://cran.r-project.org/web/packages/RPostgreSQL/index.html) R package.  RPostgreSQL is [DBI-compliant](https://github.com/rstats-db/DBI), so the connection string is the same for any other type of database engine; the biggest caveat of loading your `servercall` data into a database is the first load is almost guaranteed to require loading as text (using `colClasses = "character"` argument in R). The reason that you'll need to load the data as text is that Adobe Analytics implementations necessarily change over time; text is the only column format that allows for no loss of data (we can fix the schema later within Postgres either by using `ALTER TABLE` or by writing a view).

{% highlight R linenos %}
library(RPostgreSQL)

# Connect to database
conn = dbConnect(dbDriver("PostgreSQL"),
                 user="postgres",
                 password="",
                 host="localhost",
                 port=5432,
                 dbname="adobe")

#Set directory to avoid having to use paste to build urls
setwd("~/Downloads/datafeed/servercalls")

#Set column headers for server calls
column_headers <- read.delim("~/Downloads/datafeed/lookup/column_headers.tsv", stringsAsFactors=FALSE)

#Loop over entire list of files
#Setting colClasses to character only way to guarantee all data loads
#File formats or implementations can change over time; fix schema in database after data loaded
for(file in list.files()){
  print(file)
  df <- read.csv2(file, sep = "\t", header = FALSE, stringsAsFactors = FALSE, colClasses = "character")
  dbWriteTable(conn, name = 'servercalls', value = df, row.names=FALSE, append = TRUE)
  rm(df)
}

#Run analyze in PostgreSQL so that query planner has accurate information
dbGetQuery(conn, "analyze servercalls")
{% endhighlight %}

With this small amount of code, we've generated the table definition structure ([see here for the underlying Postgres code](https://gist.github.com/randyzwitch/e26b97d26689b6b31044)), loaded the data, and told Postgres to analyze the table to gather statistics for efficient queries. Sweet, two years of data loaded with minimal effort!

## Loading Lookup Tables Into PostgreSQL

With the server call data loaded into our database, we now need to load our lookup tables. Lucky for us, these do maintain a constant format, so we don't need to worry about setting all the fields to text, RPostgreSQL should get the column types correct.

{% highlight R linenos %}
library(RPostgreSQL)

# Connect to database
conn = dbConnect(dbDriver("PostgreSQL"),
                 user="postgres",
                 password="",
                 host="localhost",
                 port=5432,
                 dbname="adobe")

setwd("~/Downloads/datafeed/lookup/")

#Create function due to repetitiveness
#Since we're loading lookup tables with mostly same values each time, put source file in table
loadlookup <- function(tblname){
  df <- read.csv2(paste(tblname,".tsv", sep=""), sep = "\t", header = FALSE, stringsAsFactors = FALSE)
  df$file <- file
  dbWriteTable(conn, name = tblname, value = df, row.names=FALSE, append = TRUE)
}

#untar files, place in directory by day
for(file in list.files(pattern = "*.tar.gz")){
  print(file)
  untar(file)

  for(tbl in c("browser_type", "browser", "color_depth", "column_headers",
               "connection_type", "country", "event", "javascript_version",
               "languages", "operating_systems", "plugins", "referrer_type",
               "resolution", "search_engines")){

    loadlookup(tbl)

  }
}
{% endhighlight %}

**SHORTCUT**: The dimension tables that are common to all report suites don't really change over time, although that isn't guaranteed.  In the 758 days of files I loaded ([code](https://gist.github.com/randyzwitch/5ed2f4fc8574b91efd29)), the only files having more than one value for a given key were: `browser`, `browser_type`, `operating_system`, `search_engines`, `event` (report suite specific for every company) and `column_headers` (report suite specific for every company). So if you're doing a bulk load of data, it's generally sufficient to use the newest lookup table and save yourself some time. If you are processing the data every day, you can use an [upsert process](https://wiki.postgresql.org/wiki/UPSERT) and generally there will be few if any updates.

## Let's Do Analytics!!!!???!!!

_<moan>Why is there always so much ETL work, I want to data science the hell out of some data</moan>_

At this point, if you were uploading the same amount of data for the traffic my blog does (not much), you'd be about 1-2 hours into loading data, still having done no analysis. In fact, in order to do analysis, you'd still need to modify the column names and types in your `servercalls` table, update the lookup tables to have the proper column names, and maybe you'd even want to pre-summarize the tables into views/materialized views for Page View/Visit/Visitor level. Whew, that's a lot of work just to calculate daily page views.

Yes it is. But taking on a project like this isn't for page views; just use the Adobe Analytics UI!

In a future blog post or two, I'll demonstrate how to use this relational database layout to perform analyses not possible within the Adobe Analytics interface, and also show how we can skip this ETL process altogether using a [schema-on-read process](http://blog.cask.co/2015/03/schema-on-read-in-action/) with Spark.
