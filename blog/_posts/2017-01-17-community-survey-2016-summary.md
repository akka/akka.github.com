---
layout: series_post
title: "Community Survey 2016 Summary"
description: ""
author: Konrad 'ktoso' Malawski
redirect_from: /community/2017/01/17/community-survey-2016-summary
series_title: survey
series_tag: survey
category: news
tags: [community,contributing,akka,survey]
---
{% include JB/setup %}

# Community Survey 2016 summary

While it took us a while to go over the 637 replies replies from the 2016 community survey, now we’re ready to publish a small summary with our interpretation thereof. This is almost twice the amount of respondents than in the 2014 survey!

Before we jump into it, we’d like to thank all of you for taking the time to fill out our survey–thank you!

Our first question was what language you’re using Akka with. The vast majority (~80%) replied that they’re using Scala, and Java 8 (13%) is used more than previous Java versions (10%). We’re glad to see you’re adopting Java 8 over older versions. In the upcoming Akka 2.5 (which will be [compatible with 2.4](https://doc.akka.io/docs/akka/2.4/common/binary-compatibility-rules.html)) we’re polishing up our Java 8 APIs even more, so be sure to check them out once it’s out. We do know for a fact however that there’s way more Java API users out there. We know this from both our community and customer interactions, and will continue investing into the Java APIs as we always have. We explain the vast Scala dominance in this survey by the excited Scala community that was more eager to fill in such survey.

The next exciting question was about which modules you’re using. Not surprisingly, almost everyone using Akka is using Actors and TestKit (96% / 63%). Next one being Akka HTTP (70%), which got it’s [fully]({{ site.baseurl }}/blog/news/2016/11/22/akka-http-10.0.0-released.html)[ stable 10.0]({{ site.baseurl }}/blog/2016/11/22/akka-http-10.0.0-released.html) release, and has been with us in it’s previous incarnation as Spray for many years already. The next spots belonged to (in order) Streams, Cluster, Persistence and Cluster Sharding. Which leads us to believe many of you write single-node applications, yet still 40% of you do use the Cluster and its tools. With the recent work on the [new remoting "Artery"](https://doc.akka.io/docs/akka/2.4/scala/remoting-artery.html) which we plan to stabilize in early 2017, you’ll have even more reasons and gains from using Akka Cluster, such as amazing performance (benchmarked above 700.000 msg/s).

When it comes to *why* you’re using Akka, it’s clear that "safe and simple concurrency" is the key driver, closely followed by scalability / elasticity and then back-pressure and performance. Also, 11% each market that Akka helps you be productive while building truly reactive systems. These answers mostly match what we’ve seen in 2014 already, so we’re happy that we continue investing time and effort into the right–most valuable to you–things

The most popular Persistence plugin was, unsurprisingly, the Akka team maintained (though originally kick started by Martin Krasser) Akka Persistence Cassandra plugin. We agree that it’s a perfect data store for the kind of Event Sourcing workloads Akka Persistence is best suited for.

And finally a statistic that we always enjoy reading about: percent of Akka users in production in this survey. So 80%+ respondents are using Akka on their day job, and from those 77% is in production already, others heading to production very soon. Others are either evaluating, or using Akka in open source or pet projects on the side–hopefully preparing for their next full-time Akka gig :-)

We also see that the majority of you has not yet contributed to Akka but would love to do so (58%). We’ll work on making this even simpler in 2017. For example with the [Alpakka](https://github.com/akka/alpakka) initiative, or moving our docs to be markdown powered (using lightbend/paradox) which should feel more familiar and simpler to edit. We also will keep marking issues as "community" and “low priority” so you know which tickets are good ones to pick up as a first contribution.

In terms of most anticipated features that you’d like to see developed, Typed as usual takes a strong lead (49%). We’re happy to say that we’re going to work on it in 2017 and are confident this incarnation of Typed Actors will be able to express everything we wanted from them. This will be a long road, but it will be worth it. Next are features for Akka HTTP with 38% votes for it, here most likely HTTP/2 playing a huge part here, and we’re actively working on it as I write this post. And next is [Alpakka](https://github.com/akka/alpakka)[, so integrating Akka Streams with various endpoints](https://github.com/akka/alpakka), which is also in our priorities moving forward. It’s great to see the community and our plans are well aligned like this.

In addition we’d like to mention that we’ll be reworking some parts of the documentation (which 16% of you voted for) and adding a new layout to it as well as the website. In the docs rewrite we’ll explain the topics you marked as most difficult while learning Akka: splitting tasks into Actors (46%), how to operate clusters (36%) and using blocking resources (32%).

So once more, we’d like to thank all of your who participated in the survey. Thank you! As we promised during its announcement, we shipped over 100 t-shirts to randomly selected participants in the survey. 
We hope those will help you share your love for Akka, concurrency and distributed computing among your peers! 
You’ll notice we also sent out a few special *happy hakking* t-shirts, this was a secret initiative we launched in 2016 in which we rewarded top contributors to Akka for their ongoing efforts and awesome streams of pull requests. Want one of these? Simple, keep hacking, we’ll continue awarding the top-n most active / awesome contributors in 2017 (every few months).

![happy hakkers]({{ site.baseurl }}/blog/assets/happy-hakkers-2017.png)

Having that said, let 2017 begin with full steam ahead.

## Looking for hakkers!

While we have your attention here: We’re starting off the year 2017 strong, and looking to hire an additional hakker to the core Akka team. If that’s something you’d be interested in [head over to this post for more details]({{ site.baseurl }}/blog/news/2017/01/17/looking-for-hakker). We’d love to hear from you, please follow the instructions in the other post to apply.

Happy hakking!
