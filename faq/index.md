---
layout: page-full-width
title: Frequently Asked Questions
---

## Remoting

### I want to send to a connect to a remote system but it does not do anything

Make sure that you have remoting enabled on both ends: client and server. Both
do need hostname and port configured, and you will need to know the port of the
server; the client can use an automatic port in most cases (i.e. configure port
zero). If both systems are running on the same network host, their ports must
be different

If you still do not see anything, look at what the logging of [remote
life-cycle
events](http://doc.akka.io/docs/akka/current/scala/remoting.html#Remote_Events)
tells you (normally logged at INFO level) or [switch on
logging](https://github.com/akka/akka/blob/v2.0.2/akka-remote/src/main/resources/reference.conf#L66)
of all sent and received messages (logged at DEBUG level).

### What is the name of a remote actor?

When you want to send messages to an actor on a remote host, you need to know
its [full path](http://doc.akka.io/docs/akka/current/general/addressing.html),
which is of the form

    akka://system@host:1234/user/my/actor/hierarchy/path

Observe all the parts you need here:

* `system` is the remote system’s name (must match exactly, case-sensitive!)

* `host` is the remote system’s IP address or DNS name, and it must match that
  system’s configuration (i.e. `akka.remote.netty.hostname`)

* `1234` is the port number on which the remote system is listening for
  connections and receiving messages

* `/user/my/actor/hierarchy/path` is the absolute path of the remote actor in 
  the remote system’s supervision hierarchy, including the system’s guardian
  (i.e. `/user`, there are others e.g. `/system` which hosts loggers, `/temp`
  which keeps temporary actor refs used with `ask()`, `/remote` which enables
  remote deployment, etc.); this matches how the actor prints its own `self`
  reference on the remote host, e.g. in log output.

### Why are replies not received from a remote actor?

The most common reason is that the local system’s name (i.e. the
`system@host:1234` part in the answer above) is not reachable from the remote
system’s network location, e.g. because `host` was configured to be `0.0.0.0`,
`localhost` or a NAT’ed IP address.

## Microkernel

### Why doesn’t `sbt dist` copy my subproject into the kernel distribution?

For this to happen, that project’s `name` (in the project’s settings) and `id`
(argument to the `Project` factory) must match exactly.
