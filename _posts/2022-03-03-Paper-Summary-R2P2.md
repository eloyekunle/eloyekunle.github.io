---
layout: post
title: 'Notes on "R2P2: Making RPCs first-class datacenter citizens"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/atc19-kogias-r2p2_0.pdf>

**Problem**

Typical applications running in data centers are data-intensive applications with complex fan-in and fan-out connection patterns among different tiers of the application which communicate internally using RPCs. Each individual task can range from microseconds to milliseconds in execution time. These applications also frequently have strict tail-latency service-level objectives (SLO). Therefore, it is important to have efficient RPC load-balancing and processing.

A typical implementation of these RPCs involves layering RPCs on top of TCP, which is a byte-oriented, streaming transport protocol. However, RPCs are message-oriented, so this leads to a mismatch which introduces several challenges. On the end-host, this can lead to head-of-line blocking, scheduling problems, buffer-bloat, and application-level RPC parsing. Infrastructure-wide, this can lead to load imbalance, and also the inability to take advantage of network programmability.

**Approach**

This research introduces Request Response Pair Protocol (R2P2), a UDP-based transport protocol specifically designed for RPCs in a data center. They aim to elevate RPCs to become first-class network citizens by exposing the RPC abstraction to the endpoints and the network.

R2P2 decomposes an RPC workload into independent request-response pairs which are initiated by the client and are uniquely identified by a triplet of \<*src\_IP, src\_port, req\_id*\>. Since each RPC is identified by this triplet, R2P2 is able to implement a request-level load balancing policy which means responses can arrive from different machines than specified in the original destination. This also enables responses to bypass the router, and send responses directly to the client. Since each request-response pair is treated independently, there is no socket, and no head-of-line blocking caused by multiplexing RPCs on a socket.

The request/response-aware router logic of R2P2 can be implemented either in software middleboxes that add a negligible overhead or within a P4 switch ASIC pipeline which has an even more negligible overhead. R2P2 headers contain a *Policy* field that allows client applications to specify certain policies to the router or other intermediate middleboxes.

The middleboxes are also capable of implementing flow control, and there are two levels of this: one between the client and the middlebox and one between the middlebox and the servers. For the first level, R2P2 middleboxes can drop requests if they become congested. They can also implement the second level if requested by the request Policy.

This work also introduces JBSQ(n) which is a split-queue scheduling policy that utilizes a single in-network queue and bounded server queues and improves tail-latency even for microsecond-scale tasks. JBSQ splits queues between a centralized component with an unbounded queue and distributed bounded queue with configurable maximum depth *n*. JBSQ performs closer to the theoretically optimal single queue model. The value of *n* is used to control the performance and throughput of the queuing policy.

Evaluation of R2P2 on data center applications Redis and Lucene shows that the new design significantly outperforms standard load-balancing proxies in latency and throughput.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

Making it possible for RPCs to exploit programmable switches and configure per-request policies is a major strength of this paper, as well as the accompanying PoC implementations of the router logic both in software and hardware.
