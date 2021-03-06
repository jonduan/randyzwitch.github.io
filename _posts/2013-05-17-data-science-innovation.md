---
title: Innovation Will Never Be At The Push Of A Button
date: 2013-05-17T10:28:47+00:00
author: Randy Zwitch
layout: post
permalink: /data-science-innovation/
category: DataScience
description: Data Science at the push of a button is the wrong goal. Innovation is not the same as reporting, so why would the tool requirements be the same?
tags:
  - Amazon EC2
  - Data Visualization
  - Hadoop
  - NoSQL
  - Python
  - R
  - SAS
---
<blockquote class="twitter-tweet" data-conversation="none">
  <p>
    @<a href="https://twitter.com/randyzwitch">randyzwitch</a> @<a href="https://twitter.com/benjamingaines">benjamingaines</a> @<a href="https://twitter.com/usujason">usujason</a> I am envisioning the data science equivalent of an autonomous vehicle pileup.
  </p>

  <p>
    — Todd Belcher (@toddmetrics) <a href="https://twitter.com/toddmetrics/status/335030724375756800">May 16, 2013</a>
  </p>
</blockquote>

Recently, I've been getting my blood pressure up reading (marketing) articles about "big data" and "data science".  What saddens me about the whole discussion is that there is the underlying premise that what is stopping companies from "harnessing the power of big data" is just the lack of an easy-to-use, push-button tool. Respectfully, if you believe this, you should bow out of the conversation altogether.

### Math is hard and stuff.

The first article that really bothered me is titled "[Do Predictive Modelers Need to Know Math?](http://smartdatacollective.com/deanabbott/115886/do-predictive-modelers-need-know-math)" This is a provocative title from a veteran in the data mining/data science industry, and his conclusion is basically '_Yes, but not everyone on the team needs to be able to hand-solve equations._' I think that's a fair point within the context of needing to understand the mathematical concepts behind algorithms, but not needing to be bogged down by notation.

Extending that idea a little further, how far away from the math should a business be comfortable with an employee pushing the button on a machine learning algorithm? Should the CEO be building predictive models? The Intern? A Call Center Rep? For me, I think the answer falls back on the allegory of the [highly-specialized tradesperson](http://www.snopes.com/business/genius/where.asp):

> Driver: "How can you charge $100 for five minutes work? All you did was put a bolt on and turned the wrench a few times!
>
> Mechanic: "I didn't charge you for the parts, I charged you for knowing where to put the wrench..."

The value a data scientist brings to a business is not that he can push the buttons in a GUI like [rattle for R](http://rattle.togaware.com/), [Weka](http://www.cs.waikato.ac.nz/ml/weka/), [KnowledgeSeeker](http://www.angoss.com/predictive-analytics-software/products/data-analysis-software), or [SAS Enterprise Miner](http://www.sas.com/technologies/analytics/datamining/miner/). What your data scientist brings to the table is knowing the underlying assumptions that go into a model, how the algorithm works, which algorithm is appropriate for the business problem being solved and _when to know the model/algorithm has failed_.

Of all of the things listed, the experience of knowing when the model can/has failed is what you're paying the money for. That knowledge doesn't come from just pushing the GUI buttons a bunch of times. And if you're making million-dollar decisions based on an algorithm, it's worth paying the salary for a person who really understands the model.

### Hire a mathematician, get a programmer free

The next article that bothered me is a ["Business Analytics: Do we need data scientists?"](http://www.zdnet.com/debate/business-analytics-do-we-need-data-scientists/10119786/rebuttal/#skip-intro) debate over "Do we need data scientists at all?" The _No_ argument boils down to an idea that only things that can be made easy and are sufficiently developed are useful/valuable. Thus, because a general analyst can't use Excel, but rather might need to write a SQL query or write a program to put together a dataset, the problem-domain is too difficult. The _No_ debater also refers to data scientists as being "adversarial", "pretentious", project "snobbery", etc.

But here's the thing...the problem-domain isn't particularly difficult if you hire someone with above-average math proficiency. Any decent graduate program in mathematics, statistics, computer science, economics, finance, psychology and others will be using data through programming. Now, the languages may vary between Java, R, Python, Matlab, C++, SAS, Octave, Eviews or others, but the language doesn't matter, they'll learn whatever language your company is using once you hire them. They also will learn the systems you are using to store your data, whether it's a standard relational database, a NoSQL database, or a parallel processing platform like Hadoop.

How can I be certain that the math person you hire will be able to learn all that's necessary for data science? Because the type of person who likes math & programming is probably a 'system builder' type of person. The type of person who played with Legos growing up. The type of person built their own desktop computer back in the day. The type of person who thinks _How It's Made_ is much more interesting TV than mindless reality shows. The type of person who WANTS to know how a database is storing data, what new open-source technology is out there, wants to find out how many nodes they can connect together before their program won't finish any faster.

As far as the adversarial/pretentious/snobby comment, all I can say is I've never witnessed that. Everyone I know in the data science community are the nicest people, willing to share code, collaborate on ideas and talk until they lose their voice about how to solve an interesting problem.

### Data Science is about innovative research, not reporting

I've read four academic papers this week. I'm not in graduate school.

As some of you might know, I started a new position at a startup which provides real-time intelligence for the lead generation industry. As such, I've got access to billions of records of unstructured data and equally as much structured data. And as a startup, there are several warts that need to be fixed with respect to data storage. So for any given day, I might go from accessing a MySQL database, Amazon Redshift (columnar RDBMS), Amazon DynamoDB (NoSQL) and plain ol' .csv files via Excel or massive .csv files on Amazon S3. To access this data, I've used a combination of R, Python, SQL Workbench, and MySQL Workbench using OSX, Ubuntu desktop and a 'headless' Ubuntu image on Amazon EC2.

Why am I giving you about all this jibber-jabber about research papers and tools? Because the idea of building a one-size-fits-all tool to solve the problem I'm working on just doesn't make sense. And for that matter, I'm not even sure the problem I'm working on is worth solving. But that's the thing...I don't KNOW it's not worth solving, so I need to find out. I've got a quarter-billion URLs that I think I can extract information from, just to give our clients ONE more data element to use to optimize their marketing strategies.  There may be an already existing algorithm I can use, or maybe I'll try this research paper on ["word breaking"](http://research.microsoft.com/apps/mobile/publication.aspx?id=144355) I found from Microsoft Research. Once I find out the answer, if it's valuable, then I need to be able to implement my algorithm into our real-time API, because it's likely whatever language I end up using isn't going to be what our API is written in.

So if these aren't the type of problems you're working on, then maybe there is an all-in-one tool out there for you to use (and that's okay). But these are the types of edge-case problems that I think about when I think about "data science", and as such, it will always be custom and ad-hoc. There are many awesome open-source tools I will use to help me along the way, but it will never make sense to build an easy-to-use tool for a problem a few dozen companies may ever need to know the answer to.

### Use the data you have to do something extraordinary

<blockquote class="twitter-tweet">
  <p>
    If you don't 'get' something, own it. Don't dump your dumb garbage into the world.
  </p>

  <p>
    — marc maron (@marcmaron) <a href="https://twitter.com/marcmaron/status/335167001427320832">May 16, 2013</a>
  </p>
</blockquote>

I'm already 1100 words into this rant, so I'll finish up with a few admissions. Yes, "data science" is somewhat a ridiculous name for the combination of advanced analytics and data engineering that it represents. And yes, there are plenty of vendors out there pedaling hype about the grandeur of 'Big Data' and why every business MUST jump on board or be left behind.

But rather than focusing on why something is "useless" or "stupid" or "hype", just ask yourself "Can I solve the business problems I have today using the tools I currently have access to?" If the answer is yes, then great, get to work. If not, maybe you can find someone to help you get where you're going (and that person may or may not call themselves a "Data Scientist"). Either way, let's all move forward and do something extraordinary. It's the least we can do for our customers.
