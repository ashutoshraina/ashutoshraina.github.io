---
layout: post
title: NDepend Review
category: Review, Tools
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Patrick from NDepend wasgracious enough to give me a license for NDepend. I have been wanting to get my hands on such a tool for some time now, and as it turns NDepend is actually quite good. Let's have a look at what the hell am I a talking about.
<!--excerpt-->
[NDepend](http://www.ndepend.com/ "NDepend") is a tool which allows you to maintain code quality in a continuous way. I must state <em>continuous</em> with some degree of importance since that is what drives quality over a sustained period of time. The set up is fairly simple. You can download it and then install the VS plugin or use it as a stand alone tool. Let's go with the VS plugin for now. To ensure that I wasn't just checking out a demo application, I decided to pull down some medium and large open source projects and then run them through the grind.
First, [Snow]("https://github.com/Sandra/Sandra.Snow", "Sandra.Snow")(since this blog runs on Snow). The menu offers a way of creating a new NDepend project and attach it to the existing solution. We will attach to just Snow for now(leave out the other for the sake of simplicity).

Photo Goes here
![Getting Started](/stylesheets/images/posts/GettingStarted.png)

Once we are finished analysing the assemblies. We get a summary of the application level metrics like this. This is quite useful, especially once we establish the baseline.

Application Metrics
![Application Metrics](/stylesheets/images/posts/ApplicationMetrics.png)

Now, dow nto serious business. We are told that we are in violation of some rules (we haven't changed the default settings). NDepend comes with a default set of rules which are largely good and move us towards the general pit of happiness. Rules include best practices for OO design, framework usage guidelines and there are even rules on immutability. So, let's see what did we get.



    warning     Avoid public methods not publicly visible 37  methods Visibility
    warning     Fields should be declared as private 1 fields  Visibility
    warning     Fields that could have a lower visibility 1 fields  Visibility
    .....
    Many more


Hmm..these look good. Let's dig deeper.

The dependency graph is also a nice insight.

![Dependency Graph](/stylesheets/images/posts/DependencyGraph.png)

Snow is a medium sized code base so the analysis is quite good and accurate as well.

Will I use NDepend ? Probably Yes
I won't run it without configuring the rules according to my taste (naming static fields with s_ is a little bit of an overkill). It is very easy to change these rules so that's not too tough.

![Managing Rules](/stylesheets/images/posts/ManagingRules.png)

I will probably not keep it running all the time, (combined with ReSharper VS becomes a little slow). The dashboard gives you a clean and clear overview of the entire process which is ofcours nice, since you don't have to load the addin into VS.

![Dashboard](/stylesheets/images/posts/NDependDashboard.png)

Also, I don't think it makes much sense to run at every check-in. In your CI process you are better of making this a nightly thing, and view the results in the morning. Watchout for the .gitignore files though, they might need some updating.

Note : 
The review is entirely my own and not influenced by anyone.

P.S. Why is everything uppercase by default in the Dashboard Menu ? (I thought the VS fiasco was enough)