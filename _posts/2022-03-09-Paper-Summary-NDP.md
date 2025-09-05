---
layout: post
title: 'Notes on "Re-architecting datacenter networks and stacks for low latency and high performance" (NDP)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://courses.cs.duke.edu/compsci514/cps214/fall18/readings/p29-Handley.pdf>

**Problem**

Modern data center networks have evolved rapidly over the years and now provide very high capacity via Clos topologies and low switch latencies. However, the available transport protocols rarely provide matching performance, especially under congestion. When congestion occurs, packet queues build up and PFC pause frames are generated, both of which increase network latency.

Apart from low latency, applications also need to handle a network traffic pattern called the “Incast” problem. Modern application design patterns usually involve a “gateway” application farming out requests to several workers, collecting their responses, and aggregating a response. Applications such as MapReduce utilize multiple workers for performance, while Web Search applications usually have several workers holding shards of web indices. However, multiple incoming flows simultaneously arriving at an application can lead to the incast problem which can lead to packet loss and increased latency.

Another related problem is the straggler problem. When requests are farmed out, an application needs to wait for all responses to arrive before it can proceed. If responses are queued at the switch, it is important to prioritize responses from older requests since they may be blocking the request.

**Approach**

The authors presented NDP, a datacenter protocol architecture that achieves low latency and high throughput. It is able to deal with incast events and also prioritize traffic from different senders. 

To achieve low latency, switch queues must be small. However, when the queues get filled up and packets are dropped, there is no way for the sender to know and retransmit fast enough to meet the latency requirements. To prevent packet loss, lossless Ethernet pauses inbound traffic which adds queueing delay and violates low latency and high throughput goals.

NDP adopts packet trimming, which is a middle ground between packet loss and lossless and is also the same approach used by Cut Payload (CP). However, CP can suffer from congestion collapse, phase effects leading to unfair throughput, and retransmission delay due to its FIFO queuing.

NDP enhances CP by maintaining two queues \- a lower priority queue for data packets and a higher priority queue for trimmed headers, ACKS, and NACKS. This helps to achieve fast retransmission. It also performs weighted round robing between both queues which allows early feedback while avoiding congestion collapse. Finally, when a new packet arrives, the switch decides with a 50% probability to trim the new packet or the packet at the tail of the low priority queue. This helps to avoid strong phase effects.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The ideas in the paper are well-presented. I especially liked the receiver-driven transport protocol which uses pull packets to reduce packet loss and regulate sending rate. While the paper achieved its network performance goals, an evaluation of its impact on the CPU resources will be helpful as well. Public cloud providers, for example, generate revenue based on how much CPU they can rent out, so they are incentivized to reduce operations CPU usage as much as possible. As the authors also stated, if a lot of the implementation is moved from software to hardware (Ethernet NICs), then this can reduce the needed CPU resources.
