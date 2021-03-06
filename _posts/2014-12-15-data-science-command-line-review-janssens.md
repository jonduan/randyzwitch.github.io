---
title: 'Review: Data Science at the Command Line'
date: 2014-12-15T10:22:46+00:00
author: Randy Zwitch
layout: post
permalink: /data-science-command-line-review-janssens/
category: DataScience
description: Data Science at the Command Line is an easily-accessible book for anyone who wants to learn efficient techniques for data manipulation from the command line
tags:
  - Command Line
  - Data Science
  - Data Visualization
  - Python
  - R
  - Unix
---
Admission: I didn't _really know_ how computers worked until around 2012.

For the majority of my career, I've worked for large companies with centralized IT functions. Like many statisticians, I fell into a comfortable position of learning SAS in a Windows environment, had Ops people to fix any Unix problems I'd run into and DBAs to load data into a relational database environment.

Then I became a consultant at a boutique digital analytics firm. To say I was punching above my weight was an understatement. All of the sudden it was time to go into various companies, have a one-hour kickoff meeting, then start untangling the spaghetti mess that represented their various technology systems. I also needed to figure out the boutique firm's hacked together AWS and Rackspace infrastructure.

[![data-science-command-line](/wp-content/uploads/2014/12/data-science-command-line.png)](http://datascienceatthecommandline.com/)

I'm starting off this review with this admission, because my story of learning to work from the command line parallels [Data Science at the Command Line](http://datascienceatthecommandline.com/) author [Jeroen Janssens](https://twitter.com/jeroenhjanssens):

> Around five years ago, during my PhD program, I gradually switched from using Microsoft Windows to GNU/Linux...Out of necessity I quickly became comfortable using the command line. Eventually, as spare time got more precious, I settled down with a GNU/Linux distribution known as Ubuntu...
>
> - Preface, pg. xi

Because a solid majority of people have never learned anything beyond point-and-click interface (Windows or Mac), the title of the book _Data Science at the Command Line_ is somewhat unfortunate; this is a book for ANYONE looking to start manipulating files efficiently from the command line.

## Getting Started, Safely

One of the best parts of _Data Science at the Command Line_ is that it comes with a [pre-built virtual machine](http://datasciencetoolbox.org/) with 80-100 or more command line tools installed. This is a very fast and safe way to get started with the command line, as the tools are pre-installed and no matter what command you run while you're learning, you won't destroy a computer you actually care about!

Chapters 2 and 3 move through the steps of installing the virtual machine, explaining the essential concepts of the command line, some basic commands showing simple (but powerful!) ways to chain command line tools together and how to obtain data. What I find so refreshing about these two chapters by Janssens is that the author assumes zero knowledge of the command line by the reader; these two chapters are the most accessible summary of how and why to use the command line I've ever read ([Zed Shaw's CLI tutorial](http://cli.learncodethehardway.org/book/) is a close second, but is quite terse).

## The OSEMN model

The middle portion of book covers the [OSEMN model](http://www.dataists.com/2010/09/a-taxonomy-of-data-science/) (Obtain-Scrub-Explore-Model-iNterpret) of data science; another way this book is refreshing is that rather than jump right into machine learning/predictive modeling, the author spends a considerable amount of time covering the gory details of real analysis projects: manipulating data from the format you _receive_ (XML, JSON, sloppy CSV files, etc.) and taking the (numerous) steps required to get the format you _want_.

By introducing tools such as [csvkit](https://csvkit.readthedocs.org/en/0.9.0/) (csv manipulation), [jq](http://stedolan.github.io/jq/) (JSON processor), and classic tools such as [sed](https://www.gnu.org/software/sed/manual/sed.html) (stream editor) and [(g)awk](http://www.gnu.org/software/gawk/manual/gawk.html), the reader gets a full treatment of how to deal with malformed data files (which in my experience are the only type available in the wild!) . Chapter 6 ("Managing Your Data Workflow") is also a great introduction into [reproducible research](http://en.wikipedia.org/wiki/Reproducibility#Reproducible_research) using [Drake](http://blog.factual.com/introducing-drake-a-kind-of-make-for-data) (Make for Data Analysis). This is an area that I will personally be focusing my time on, as I tend to run a lot of one-off commands in HDFS and as of now, just copy them into a plain-text file. Reproducing = copy-paste in my case, which defeats the purpose of computers and scripting!

## An Idea Can Be Stretched Too Far

Chapters 8 and 9 cover Parallel Processing using [GNU Parallel](http://www.gnu.org/software/parallel/) and Modeling Data respectively. While GNU Parallel is a tool I could see using sometime in the future, I do feel like building models and creating visualizations straight from the command line is getting pretty close to just being a parlor trick. Yes, it's obviously possible to do such things (and the author even wrote his own [command line tool Rio](https://github.com/jeroenjanssens/data-science-at-the-command-line/blob/master/tools/Rio) for using R from the command line), but with the amount of iteration, feature building and fine-tuning that goes on, I'd rather use [IPython Notebook](http://ipython.org/notebook.html) or [RStudio](http://www.rstudio.com/) to give me the flexibility I need to really iterate effectively.

## A Book For Everyone

As I mentioned above, I really feel that _Data Science at the Command Line_ is a book well suited for anyone who does data analysis. Jeroen Janssens has done a fantastic job of taking his original ["7 command-line tools for data science"](http://jeroenjanssens.com/2013/09/19/seven-command-line-tools-for-data-science.html) blog post and extending the idea to a full-fledged book. This book has a prominent place in my work library next to [Python for Data Analysis](http://shop.oreilly.com/product/0636920023784.do) and in the past two months I've referred to each book at roughly the same rate. For under $30 for paperback at Amazon, there's more than enough content to make you a better data scientist.
