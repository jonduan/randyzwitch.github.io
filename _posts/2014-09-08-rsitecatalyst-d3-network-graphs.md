---
title: Visualizing Website Structure With Network Graphs
date: 2014-09-08T06:40:38+00:00
author: Randy Zwitch
layout: post
permalink: /rsitecatalyst-d3-network-graphs/
category: Analytics
description: Visualizing the structure of your website is easy using RSiteCatalyst and network graphs from d3Network in R.
tags:
  - Adobe Analytics
  - Data Visualization
  - Omniture
  - R
  - RSiteCatalyst
---
Last week, [version 1.4 of RSiteCatalyst](http://randyzwitch.com/rsitecatalyst-version-1-4-release-notes/) was released, and now it's possible to get site pathing information directly within R. Now, it's easy to create impressive looking network graphs from your Adobe Analytics data using [RSiteCatalyst](http://cran.r-project.org/web/packages/RSiteCatalyst/index.html) and [d3Network](http://cran.r-project.org/web/packages/d3Network/index.html). In this blog post, I will cover simple and force-directed network graphs, which show the pairwise representation between pages. In a follow-up blog post, I will show how to visualize longer paths using [Sankey diagrams](http://www.sankey-diagrams.com/), also from the d3Network package.

## Obtaining Pathing Data With QueuePathing

Although the `QueuePathing()` function is new to RSiteCatalyst, its syntax should feel familiar (even with all of the breaking changes we made!). In the case of creating our network graphs, we want to download all pairwise combinations of pages, which is easy to do using the `::anything::` operator:

{% highlight R linenos %}
library("RSiteCatalyst")
library("d3Network")

#### Authentication
SCAuth("username", "secret")

#### Get Pathing data using ::anything:: wildcards
# Results are limited by the API to 50000
pathpattern <- c("::anything::", "::anything::")

queue_pathing_pages <- QueuePathing("zwitchdev",
                                    "2014-01-01",
                                    "2014-08-31",
                                    metric="pageviews",
                                    element="page",
                                    pathpattern,
                                    top = 50000)
{% endhighlight %}

Because we are using a pathing pattern of `("::anything::", "::anything::")`, the data frame that is returned from this function will have three columns: `step.1`, `step.2` and `count`, which is the number of occurrences of the path.

## Plotting Graph Using d3SimpleNetwork

Before jumping into the plotting, we need to do some quick data cleaning. Lines 1-5 below are optional; I don't set the Adobe Analytics s.pageName on each of my blog pages (a worst practice if there ever was one!), so I use the `sub()` function in Base R to strip the domain name from the beginning of the page. The other data frame modification is to remove the `'Entered Site'` and `'Exited Site'` from the pagename pairs. Although this is important information generally, these behaviors aren't needed to show the pairwise relationship between pages.

{% highlight R linenos %}
#Optional step: Cleaning my pagename URLs to remove to domain for graph clarity
queue_pathing_pages$step.1 <- sub("http://randyzwitch.com/","",
                                  queue_pathing_pages$step.1, ignore.case = TRUE)
queue_pathing_pages$step.2 <- sub("http://randyzwitch.com/","",
                                  queue_pathing_pages$step.2, ignore.case = TRUE)

#### Remove Enter and Exit site values
#This information is important for analysis, but not related to website structure
graph_links <- subset(queue_pathing_pages, step.1 != "Entered Site" & step.2 != "Exited Site")

#### First pass - Simple Network
# Setting standAlone = TRUE creates a full HTML file to view graph
# Set equal to FALSE to just get the d3 JavaScript
simpleoutput1 = "C:/Users/rzwitc200/Desktop/simpleoutput1.html"
d3SimpleNetwork(graph_links, Source = "step.1", Target = "step.2", height = 600,
                width = 750, fontsize = 12, linkDistance = 50, charge = -50,
                linkColour = "#666", nodeColour = "#3182bd",
                nodeClickColour = "#E34A33", textColour = "#3182bd", opacity = 0.6,
                standAlone = TRUE, file = simpleoutput1)
{% endhighlight %}

Running the above code results in the following graph:

<iframe src="http://randyzwitch.com/wp-content/uploads/2014/09/simpleoutput1.html" width="750" height="500" frameborder="0" scrolling="no"></iframe>

Hmmm...looks like a blob of spaghetti, a common occurrence when creating graphs. We can do better.

## Pruning Edges From The Graph

There are many <a title="Pruning Edges from Network" href="http://link.springer.com/chapter/10.1007%2F978-3-642-31830-6_13" target="_blank">complex algorithms for determining how to prune edges/nodes from a network</a>. For the sake of simplicity, I'm going to use a very simple algorithm: each path has to occur more than 5 times for it to be included in the network. This will prune roughly 80% of the pairwise page combinations while keeping ~75% of the occurrences. This is simple to do using the `subset()` function in R:

{% highlight R linenos %}
#### Second pass: thin the spaghetti blob!
#Require path to happen more than some number of times (count > x)
#What constitutes "low volume" will depend on your level of traffic
simpleoutput2 = "C:/Users/rzwitc200/Desktop/simpleoutput2.html"
d3SimpleNetwork(subset(graph_links, count > 5), Source = "step.1", Target = "step.2", height = 600,
                width = 750, fontsize = 12, linkDistance = 50, charge = -100,
                linkColour = "#666", nodeColour = "#3182bd",
                nodeClickColour = "#E34A33", textColour = "#3182bd", opacity = 0.6,
                standAlone = TRUE, file = simpleoutput2)
{% endhighlight %}

The result of pruning the number of edges is a much less cluttered graph:

 <iframe src="http://randyzwitch.com/wp-content/uploads/2014/09/simpleoutput2.html" width="750" height="500" frameborder="0" scrolling="no"></iframe>

Even with fewer edges in the graph, we still lose some of the information about the pages, since we don't know what topics/groups the pages represent. We can fix that using a slightly more complex version of the d3Network graph code.

## Force-directed graphs

The graphs above outline the structure of randyzwitch.com, but they can be improved by adding color-coding to the nodes to represent the topic of the post, as well as making the edges thicker/thinner based on how frequently the path occurs. This can be done using the `d3ForceNetwork()` function like so:

{% highlight R linenos %}
#### Force directed network

#Limit to more than 5 occurence like in simple network
fd_graph_links <- subset(graph_links, count > 5)

#Get unique values of page name to create nodes df
#Create an index value, starting at 0
fd_nodes <- as.data.frame(unique(c(fd_graph_links$step.1, fd_graph_links$step.2)))
names(fd_nodes) <- "name"
fd_nodes$nodevalue <- as.numeric(row.names(fd_nodes)) - 1

#Create groupings for node colors
#This is user-specific in terms of how to create these groupings
#Due to few number of pages/topics, I am manually coding this

grouping <- function(string){

  if(grepl("(hadoop|hive|pig)",string, perl=TRUE)){
    return(1)
  }else if(grepl("(julia|uaparser-jl)",string, , perl=TRUE)){
    return(2)
  }else if(grepl(("[r]?sitecatalyst|adobe-analytics|omniture"),string, perl=TRUE)){
    return(3)
  }else if(grepl("(wordpress|twenty-eleven|scrappy)",string, perl=TRUE)){
    return(4)
  }else if(grepl("data-science|ec2",string, perl=TRUE)){
    return(5)
  }else if(grepl("python",string, perl=TRUE)){
    return(6)  
  }else if(grepl("(digital-analytics|google-analytics|web-analyst)",string, perl=TRUE)){
    return(8)
  }else if(grepl("(macbook|iphone)",string, perl=TRUE)){
    return(9)
  }else if(grepl("(randyzwitch|about|page)",string, perl=TRUE)){
    return(10)
  }else if(grepl("(rstudio|rcmdr|r-language|jsonlite|r-language-oddities|tag/r|automated-re-install-of-packages-for-r-3-0|learning-r-sas|creating-dummy-variables-data-frame-r)",string, perl=TRUE)){
    return(7)
  }else{
    return(11)
  }

}

#Create group column
fd_nodes$group <- sapply(fd_nodes$name, grouping)

#Append numeric nodeid to pagename
fd_graph_links <- merge(fd_graph_links, fd_nodes[,1:2], by.x="step.1", by.y="name")
names(fd_graph_links) <- c("step.1", "step.2", "value", "source")

fd_graph_links <- merge(fd_graph_links, fd_nodes[,1:2], by.x="step.2", by.y="name")
names(fd_graph_links) <- c("step.1", "step.2", "value", "source", "target")

d3output = "C:/Users/rzwitc200/Desktop/fd_graph.html"
# Create force-directed graph
d3ForceNetwork(Links = fd_graph_links, Nodes = fd_nodes, Source = "source",
               Target = "target", NodeID = "name",
               Group = "group", opacity = 0.8, Value = "value",
               file = d3output,
               charge = -90,
               fontsize=12)
{% endhighlight %}

Running the code results in the following force-directed graph:

<iframe src="http://randyzwitch.com/wp-content/uploads/2014/09/fd_graph.html" width="750" height="500" frameborder="0" scrolling="no"></iframe>

## Interpretation

I'm not going to lie, all three of these diagrams are hard to interpret. Like wordclouds, network graphs can often be visually interesting, yet difficult to ascertain any concrete information. Network graphs also have the tendency to reinforce what you already know (you or someone you know designed your website, you should already have a feel for its structure!).

However, in the case of the force-directed graph above, I do see some interesting patterns. Specifically, there are a considerable number of nodes that aren't attached to the main network structure. This may be occurring due to my method of pruning the network edges. More likely is that these disconnected nodes represent "dead-ends" in my blog, either because few pages link to them, there are technical errors, these are high bounce-rate pages or represent one-off topics that satiate the reader.

In terms of action I can take, I can certainly look up the bounce rate for these disconnected pages/nodes and re-write the content to make it more 'sticky'. There's also the case of the way my "Related Posts" plugin determines related pages. As far as I know, it's quite naive, using the existing words on the page to determine relationships between posts. So one follow-up could be to create an actual recommender system to better suggest content to my readers. Perhaps that's a topic for a different blog post.

Regardless of the actions I'll end up taking from this information, hopefully this blog post has piqued some ideas of how to use RSiteCatalyst in a non-standard way, to extend the standard digital analytics information you are capturing with Adobe Analytics into creating interesting visualizations and potential new insights.

#### Example Data

_For those of you who aren't Adobe Analytics customers (or are, but don't have API access), here are the [data from the `queue_pathing_pages` data frame](/wp-content/uploads/2014/09/queue_pathing_pages.csv) above. Just read this data into R, then you should be able to follow along with the `d3Network` code._
