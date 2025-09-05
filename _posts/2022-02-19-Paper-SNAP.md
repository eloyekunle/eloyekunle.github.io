---
layout: post
title: 'Notes on "Snap: a Microkernel Approach to Host Networking"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://courses.grainger.illinois.edu/CS598HPN/fa2020/papers/snap.pdf>

**Problem**

The rise of cloud computing and high-performance distributed systems has led to increased demands for more efficient and lower latency communication. Networking software for data center environments may need to make different design trade-offs than general-purpose software. 

For Google, one of the main challenges was the lengthy development and release cycles of conventional kernel-based software. Originally, deploying new network functionality and performance optimizations would require machine reboots and drain of running applications, and the pool of developers with kernel development experience was quite small.

**Approach**

Snap is a user-space networking system built and used internally by Google, which solves the rapidly evolving networking needs. User-space software development can be done much faster than at the kernel level, and also makes possible transparent upgrades of network services without affecting running applications. If Snap was implemented as a form of library, then updates to Snap will require updates and reloads of the running applications themselves, and this was not acceptable.

The solution is to place Snap outside the kernel and applications in a microkernel approach, and applications communicate with Snap using shared memory reads and writes. The control plane is orchestrated via RPCs, and data plane operations are handled by pluggable engines.

The transport and communication stack in Snap is called *Pony Express*. It is built from scratch and implements reliability, congestion control, optional ordering, flow control, and execution of remote data access operations. It offers asynchronous operation-level commands and completions and it supports both two-sided and one-sided operations. Pony Express has two layers for its transport logic: an upper layer implements state machines for application-level operations, and a lower layer implements reliability and congestion control.

Upgrades in Snap take place in two phases. There is a *brownout phase*, in which a new version of Snap is created and there is a background transfer of the current state to the new version, followed by a short *blackout phase* which cuts over to the new version. Applications do not notice the blackout phase, because even though packet losses may occur during this period, the transport protocols tolerate it as if it were congestion-caused packet loss.

The internal wire protocol is periodically extended and modified, so multiple versions may exist in the fleet at the same time. They implement a tcp socket that advertises the available wire protocol version when connecting to a remote engine, and the lowest common denominator is used. Older versions are discontinued once the fleet moves on to newer versions.

Google is able to have a weekly deployment of Snap to their fleet, which is much better than the 1 or 2 month release period for typical kernel updates to get deployed.

**Strengths, Weaknesses & Possible Follow-Up Ideas**

Management of incremental development and deployment with minimal or no downtime is a critical component of data center operations, and upgrades must be as transparent as possible to user applications. Snap does this well, while also delivering better performance as a side-effect. This is a solution that could only have been proposed by a company at Google’s scale due to the huge amount of development work involved. It will be great to see how other data center operators choose to solve similar problems in their data centers, since the speed of deployment may not necessarily justify the expense of building a new transport stack from scratch.
