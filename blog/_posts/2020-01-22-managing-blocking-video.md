---
layout: post
title: Managing Blocking in Akka video
author: Arnout Engelen
short: A short video about how to manage blocking in Actors
category: news
tags: [videos]
youtubeId: xBVKJUzYD_Q
---

{% include youtubePlayer.html id=page.youtubeId %}

A four-minute video explaining why it is bad to block inside an actor, and how
you can use custom dispatchers to manage blocking when you cannot avoid it.

The slides and sample code are available [on GitHub](https://github.com/raboof/akka-blocking-dispatcher).

For more information see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/dispatchers.html#blocking-needs-careful-management)
