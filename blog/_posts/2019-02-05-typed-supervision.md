---
layout: series_post
title: "Typed Supervision: why the changes?"
description: "Supervision works differently in Akka Typed compared to the original actor implementation, in this excerpt from the course Programming Reactive Systems lecturer Roland Kuhn explains why."
author: "Dr. Roland Kuhn"
series_title: Programming Reactive Systems
series_tag: course
category: article
tags: [actor,typed,course]
---
{% include JB/setup %}

Supervision works differently in Akka Typed compared to the original actor implementation, in this excerpt from the course Programming Reactive Systems lecturer Roland Kuhn explains why.

The actor model became known at first for enabling highly reliable systems like telecommunication network equipment. As I argued in 2013 [resilience](https://www.infoq.com/articles/resilient-software-with-akka) is the result of loose coupling between encapsulated components, accepting failure as an expected part of the system’s behavior and addressing it using supervision. This has been the guiding principle of [Akka](https://akka.io/) since its inception, deeply embedded in its core API since version 2.0 with its mandatory supervision. Akka Typed adds static type-checking and takes a more functional approach to achieve more safety in expressing actor programs, and as part of these changes supervision has also been refined.

## Loose Coupling

In untyped Akka actors, supervision is implemented by suspending the failed actor, sending notice of the failure to its supervisor who makes a decision, and then sending the verdict back to the failed actor. This model is quite intuitive since it resembles human failure management processes with its communication and decision making processes, but it also couples an actor to its supervisor in some undesirable ways.

Firstly, it requires special machinery to keep the failed actor from processing messages until the verdict comes back—in the meantime incoming messages pile up in the mailbox, the more so the busier the supervisor is. The throughput of an actor can thus be limited by the responsiveness of its supervisor to failure notifications.

Secondly, it confers intimate information about each failure to the supervisor who then makes the decision while having full access to its own internal state. This may sound desirable but it also bears the risk of coupling the behavior of the supervisor to the details of its child actors’ failures, in other words, if abused this may lead to those kinds of cascading failures that the actor model was designed to prevent.

## Extensibility

Another critique of untyped Akka actor supervision is that its behavior allows only limited flexibility, for example forbidding the direct implementation of back-off schemes—these must be emulated by terminating the failing actor and re-creating it at the appropriate time. Basing supervision on a commonly useful platform would enable these and other failure management schemes to be implemented in a reusable fashion.

## The New Implementation

The aforementioned weaknesses are addressed in Akka Typed by expressing supervision not as a special API within the supervisor but as a behavior decorator for the child actor: supervision can be installed by the supervisor or by the implementation of the actor itself and it is evaluated within the context of the actor and not its supervisor. When a failure occurs, the decision on what to do is taken locally, no information about the details is sent to the supervisor. The behavior decorator can choose to switch the actor into a “deaf” state of dropping messages for a while, it could enqueue incoming messages for later consumption, it could directly restart the actor or terminate it. The supervisor only learns of termination by default, unless the supervision logic decides to send normal actor messages as part of the decision-making process.

This scheme is not only more flexible and leads to better encapsulation, it also greatly simplifies the internal workings and reduces the number of concepts Akka users encounter while learning about the actor lifecycle—it is simpler and more intuitive to remove the actor’s suspended state and only deal with creation and termination. And this measure also brings Akka Typed more perfectly in line with the original Actor Model from 1973.

## Learn more in Programming Reactive Systems

This post shines a tiny spotlight on one of the many aspects around the implementation of reactive systems that are discussed in the course [Programming Reactive Systems](https://www.edx.org/course/programming-reactive-systems) on edX as [announced earlier](https://akka.io/blog/2019/02/04/programming-reactive-systems). If you want to learn more you are more than welcome to enroll in this free course brought to you by former Akka team lead and co-author of the Reactive Manifesto [Dr. Roland Kuhn](https://www.rolandkuhn.com/), long-time Akka team and Reactive Streams expert group member [Konrad Malawski](https://www.kto.so/), and [Scala Center](https://scala.epfl.ch/) director of education [Julien Richard-Foy](http://julien.richard-foy.fr/).

