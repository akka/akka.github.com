---
layout: post
title: "Akka Community update July 2016"
description: ""
author: Konrad 'ktoso' Malawski
redirect_from: /community/2016/07/05/akka-community-update-july-2016
category: news
tags: [community,contributing,akka]
---
{% include JB/setup %}

Dear hakkers,

With great joy and pleasure we bring to you... the rebooted Akka team blog! 

This blog aims to provide you with high quality technical content straight from the core team. We’ll go into more detail about "why this blog?", and how it relates to the other blogs or news pages we have in a section below. Since this is our first blog post here, let’s take a second to retrospect on the community aspect of what we’ve been doing in the last year, and where we’re headed next.

To not leave you waiting for some actual technical meat, we’ve decided to already publish the first of a series of posts about Akka Streams, you can read it here: [Threading & Concurrency in Akka Streams Explained (part I)]({{ site.baseurl }}/blog/2016/07/06/threading-and-concurrency-in-akka-streams-explained).

## Contributing highly encouraged!

When we merged Akka Streams into mainline Akka in the 2.4.2 release in February earlier this year and announced it as a stable module ([read up here what stable and experimental mean](https://doc.akka.io/docs/akka/2.4/common/binary-compatibility-rules.html)) it also meant a transition into a more stable development cycle of the core library. For library authors and the wider community though, that moment marked the beginning of rapid expansion and evolution of their libraries. We saw multiple excellent projects based on top of Akka Streams spring into life in the last months.

We’re also continuously working on making it simpler to contribute. For example, since quite some time we’ve been using the tags to mark easy to contribute issues on our issue tracker. Today we renamed that tag to [community](https://github.com/akka/akka/labels/community), which means that those are nice tickets to be contributed by the community, as the core team is unlikely to spend much time on them in the near future. We also have [nice-to-have (low-priority)](https://github.com/akka/akka/issues?utf8=%E2%9C%93&q=is%3Aopen%20is%3Aissue%20label%3A%22nice-to-have%20(low-prio)%22%20) and [documentation](https://github.com/akka/akka/issues?q=is%3Aopen+is%3Aissue+label%3At%3Adocs) tags, so you should be able to find a ticket just right for your first contribution.

> We also refreshed the [CONTRIBUTING.md](https://github.com/akka/akka/blob/master/CONTRIBUTING.md) guide to be more accessible, and explain in more detail the process and various tags that we use to mark issues etc. We hope you'll find it helpful! As always, any improvements to it are very welcome.

## Initiatives already in progress, feel free to join!

We also decided to make it simpler to contribute things which normally wouldn’t fit the core Akka project by creating the [akka/akka-stream-contrib](https://github.com/akka/akka-stream-contrib) repository. It’s a repository, similar to akka-contrib, however focused distinctly on extending Akka Streams with custom  Sources, Flows, Sinks or other stages that can help in daily routine development with Akka Streams, even if they don’t fit the core project as such. One might also think about it as an incubator, where ideas might want to prove themselves first. We’re already seeing a nice update in contributions there, but we’d like to see even more.

In the same vein, we’re looking at easing contributing to [Akka HTTP](https://github.com/akka/akka/issues?q=is%3Aopen+is%3Aissue+label%3At%3Ahttp), including more tech integrations by lifting some of the restrictive rules and lifecycle the core Akka maintains. We’ve already seen a tremendous uptake in contributions to Akka HTTP, and would like this trend to continue. In order to keep up the momentum we’re considering loosening up its release cycle from the core Akka library. This is a step we’ll want to discuss with both the community and new committers we’ll want to include to the project (much like is the case with Reactive Kafka now). On that note, we’d like to announce that we have explicit plans to spend a number of weeks specifically on HTTP in the coming sprints. Among our focus will be an HTTP/2 Proof-of-Concept as well as additional performance work specifically aiming at the short-lived-connections cases.

On the integration side of things, we’ve seen the community do an awesome job at integrating Akka (and especially Akka Streams) with some major and interesting other technologies. The most interesting one we found to be the Kafka integration, started and maintained by Krzysztof from [SoftwareMill](https://softwaremill.com/). A few months ago together with the existing team of that project, we decided to move it under the Akka organisation, with retaining all of the existing core team and augmenting it with help from the Akka core team, thus [akka/reactive-kafka](https://github.com/akka/reactive-kafka) was created, with its main committees Krzysztof (from SoftwareMill) and Alexey still being strongly involved in it. We anticipate, and would love to see similar storied in the future, such that we’re able to provide the best integrations with other technologies as we possibly can, and also foster and grow the community around them. The Reactive Kafka project is currently in a *Milestone* phase, so expect a proper announcement blog post once we’re happy with it’s shape and are ready to release it as stable. In the meantime, contributions are *very* welcome there–especially if you’re interested in Kafka!

Reactive Kafka is not the only project that the Akka team has taken under it’s wings and extended support recently. We recently included the Akka Persistence plugins for [Cassandra](https://github.com/akka/akka-persistence-cassandra) as well as [DynamoDB](https://github.com/akka/akka-persistence-dynamodb) under the Akka organisation on github and provide support and maintenance of those projects. We expect to have more integrations like these in the future, and this means we’ll need *your* expertise in those various domains to help us out, by reporting, contributing and even leading some of these projects. We can’t wait to see the Akka ecosystem grow even stronger.

## Unparalleled transparency

Last but not least, since almost a year now we’ve been publishing results of our interesting design sessions as well as sprint plans in [akka/akka-meta](https://github.com/akka/akka-meta), so if you’re curious what the core team is up to you can check it out there. Our hope in publishing these plans is that we keep everyone in the community in the loop, and also encourage contributing fixes if an area you’re interested in is not in the focus of the core team in a given sprint.

We also encourage you to join our gitter channel on giter.im/akka/akka for discussions about *using* Akka, where a vibrant community is ready to help you out or debate around anything related Akka really. Or you can join the [gitter.im/akka/dev](https://gitter.im/akka/dev) for discussions around *developing* Akka itself–this is also where the core team spends most of their time. There are more channels available for the other Akka related projects, so make sure to search gitter for those as well (e.g. [gitter.im/akka/reactive-kafka](https://gitter.im/akka/alpakka-kafka)).

Moving forward we’ll want to include more of the very strong community members we have as committers of various parts of Akka, such that the PR queue won’t grow too large and also as a form of recognition of their skills and efforts put into the project in the past (and future). We’ll be working on figuring out that process, so please look forward to it.

	

## Scala Days & Hakker Dinner

A few weeks ago in Berlin, right after Martin Odersky’s excellent keynote on ScalaDays about the future of Scala, we got together with some active Akka contributors on what we called the hakker dinner and beer, for those who enjoy it. It was a great chance to match the online nicknames with actual faces, form a more personal bond and discuss both all things things Akka and just some other random things as well.

We hope this kind of get together will become our small tradition, and hope to see *you*–future or current hakker–on the next one!

![hakker dinner]({{ site.baseurl }}/blog/assets/hakker-dinner-2016-berlin.jpg)

## This blog’s relation to other blogs

The [akka.io/news](https://akka.io/news/) site will remain unchanged, as it hosts only release announcements and important roadmap updates. 

The [letitcrash.com](https://letitcrash.com/) blog will be kept as-is, which is mostly as archive of old yet still very relevant posts as well as an aggregator of links to interesting blog posts. We do not plan to maintain it from now on though, as the need for aggregating Akka / Scala related blogs is nowadays served very well by the excellent "this week in #scala" `http://www.cakesolutions.net/teamblogs` series by Cake Solutions as well as the [ScalaTimes](https://scalatimes.com/) by SoftwareMill, thus the need for such re-blogging has lessened significantly. 

The [lightbend.com blog](https://www.lightbend.com/blog) remains active and focused on delivering top notch webinars, case-studies, white papers and more - many of which contain Akka content so it is highly recommended to keep an eye on it as well.
