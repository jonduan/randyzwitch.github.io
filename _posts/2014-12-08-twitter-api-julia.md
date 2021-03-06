---
title: Introducing Twitter.jl
date: 2014-12-08T17:12:58+00:00
author: Randy Zwitch
layout: post
permalink: /twitter-api-julia/
category: DataScience
description: Twitter.jl is a near-complete wrapper/client of the Twitter REST API v1.1. Both GET and POST methods are provided for the ability to post and download data.
tags:
  - Julia
  - NLP
  - Twitter
---
This is possibly the latest "announcement" of a package ever, given that [Twitter.jl](https://github.com/randyzwitch/Twitter.jl) has existed on [METADATA](https://github.com/JuliaLang/METADATA.jl "Julia METADATA") for nearly a year now, but that's how things go sometimes. Here's how to get started with Twitter.jl.

## Hello, World!

If 'Hello, World!' is the canonical example of getting started with a programming language, the Twitter API is becoming the first place to start for people wanting to learn about APIs. Authenticating with the Twitter API using Julia is similar to using the R or Python packages, except that rather than doing the OAuth "dance", Twitter.jl takes all four authentication values in one function:

{% highlight julia linenos %}
using Twitter

apikey = "q8Qw7WJTVP..."
apisecret = "FIichPpGJxiOssN..."
accesstoken = "98689850-v0zZNr..."
accesstokensecret = "w7bDg9K0c493T..."

twitterauth(apikey,
            apisecret,
            accesstoken,
            accesstokensecret)
{% endhighlight %}

All four of these values can be found after registering at the [Twitter Developer page](https://dev.twitter.com/) and creating an application. Having all four values in your script is less secure than just providing the api key and api secret, but in the future, I'll likely implement the full OAuth "handshake". One thing to keep in mind with this function as it currently works is that no validation of your credentials is performed; the only thing this function does is define a global variable `twittercred` for later use by the various functions that create the OAuth headers. To shout "Hello, World!" to all of your Twitter followers, you can use the following code:

{% highlight julia linenos %}
post_status_update("Hello, World!")
{% endhighlight %}

## General Package/Function Structure

From the example above, you can see that the function naming follows the [Twitter REST API](https://dev.twitter.com/rest/public) naming convention, with the HTTP verb first and the endpoint as the remainder of the function name. As such, it's a good idea at this early package state to have the Twitter documentation open while using this package, so that you can quickly find the methods you are looking for.

For each function/API endpoint, I've gone through and determined which parameters are required; these are required arguments in the Julia functions. For all other options, each function takes a second optional `Dict{String, String}` for any option shown in the Twitter documentation. While this Dict structure allows for ultimate flexibility (and quick definition of functions!), I do realize that it's less than optimal that you don't know what optional arguments each Twitter endpoint allows.

As an example, suppose you wanted to search for tweets containing the hashtag `#julialang`. The minimum function call is as follows:

{% highlight julia linenos %}
julia_tweets = get_search_tweets("#julialang")
{% endhighlight %}

By default, the API will return the 15 most recent tweets containing the `#julialang` hashtag. To return the most recent 100 tweets (the maximum per API 'page'), you can pass the "count" parameter via the `Options` Dict:

{% highlight julia %}
julia_tweets_100 = get_search_tweets("#julialang"; options = {"count" => "100"})
{% endhighlight %}

## Composite Types and DataFrames definitions

The Twitter API is structured into 4 return data types ([Places](https://dev.twitter.com/overview/api/places), [Users](https://dev.twitter.com/overview/api/users), [Tweets](https://dev.twitter.com/overview/api/tweets), and [Entities](https://dev.twitter.com/overview/api/entities)), and I've mimicked these types using Julia [Composite Types](http://julia.readthedocs.org/en/latest/manual/types/#composite-types). As such, most functions in Twitter.jl return an array of specific type, such as `Array{TWEETS,1}` from the prior `#julialang` search example. The benefit to defining custom types for the returned Twitter data is that rudimentary DataFrame methods have also been defined:

{% highlight julia linenos %}
df = DataFrame(julia_tweets_100)
{% endhighlight %}

I describe these DataFrames as 'rudimentary' as they parse the top level of JSON into columns, which results in some DataFrame columns having complex data types such as `Dict()` (and within the `Dict()`, nested Dicts!). As a running theme in this post, this is something I hope to get around to improving in the future.

## Want to Get Started Developing Julia? Start Here!

One of the common questions I get asked is how to get started with Julia, both from a learning perspective and from a package development perspective. Hacking away on the core Julia codebase is great if you have the ability, but the code can certainly be intimidating (the people are quite friendly though). Creating a package isn't necessarily hard, but you have to think about an idea you want to implement. The third alternative is...

...improve the Twitter package! If you go to the [GitHub page for Twitter.jl](https://github.com/randyzwitch/Twitter.jl), you'll see a long list of TODO items that need to be worked on. The hardest part (building the OAuth headers) has already been taken care of. What's left is [re-factoring the code for simplification](http://randyzwitch.com/julia-metaprogramming-refactoring/), factoring out the [OAuth code in general into a new Julia library](https://github.com/randyzwitch/OAuth.jl) (also partially started), then building the Streaming API functions, cleaning up the DataFrame methods to remove the `Dict` column types, paging through API results...and so-on.

So if any of you are on the sidelines wanting to get some practice on developing packages, without needing to worry about learning Astrophysics first, I'd love to collaborate. And if any Julia programming masters want to collaborate, well that's great too. All help and pull requests are welcomed.

In the meantime, hopefully some of you will find this package useful for natural language processing, social networking analysis or even creating bots 😉
