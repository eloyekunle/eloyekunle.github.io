---
layout: post
title: 'Notes on "Data Center TCP (DCTCP)"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://people.csail.mit.edu/alizadeh/papers/dctcp-sigcomm10.pdf>

**Problem**

Three important requirements applications have for the datacenter network are low latency for short flows, high burst tolerance, and high utilization for long flows. The first two requirements are a result of the Partition/Aggregate design pattern in web applications, where requests are broken into pieces and their processing is distributed to workers in lower layers, and then aggregated up the tree to produce a result. Since the end results have tight deadlines, each individual task has a tighter deadline. Tasks that don’t make the deadline get canceled, and this can affect the accuracy of the end result. The last requirement is due to the need to continuously update the internal data structures of these applications.

First, the authors measured a production cluster to understand the nature of DC traffic and help understand impairments that cause high application latencies. Their analysis split cluster traffic into three \- query traffic, short message traffic, and continuous background traffic.

Most switches have shallow packet buffers, and they discovered that this causes three specific problems: incast, queue buildup, and buffer pressure.

**Approach**

In order to solve the above problems, the authors introduced Data Center TCP (DCTCP), with the goal of fulfilling application requirements and using commodity shallow buffered switches. It is designed to work with small queues without loss of throughput.

DCTCP works by reacting to congestion in proportion to the extent of congestion. At each switch, it sets the Congestion Experienced (CE) codepoint of arriving packets if the queue occupancy is greater than a threshold K upon its arrival. When the receiver sees a marked packet, it sets the ECN-Echo flag in the corresponding ACK sent to the sender. It also uses Delayed ACKs by sending one cumulative ACK for every *m* consecutively received packets that have the same codepoint value.

The sender tries to estimate the probability that the queue size at the switch is greater than K based on the received ACKs. This allows the senders to gently start reducing their windows when the queue exceeds K.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

DCTCP only requires a very minimal change to TCP and only a parameter change to the switches, and this simplicity has helped to improve its adoption in data center environments. Evaluations also show significant performance benefits when compared to TCP.

The authors state that if the traffic sent in the first RTT overflows the buffers, then this can lead to timeouts. Another side effect can be a severe oscillation of the actual queue size before the sender reacts to the RTT delay. 

One attempt at resolving these weaknesses was presented by Chen et al (2013). They propose splitting the single threshold K into two thresholds K1 \< K \< K2, such that the CE flag is set when queue size is K1 before actual congestion is experienced, and stops at K2 before the queue size gets too low. Analysis of this double-threshold DCTCP (DT-DCTCP) shows that it achieves a smaller queue, and has a queue length less sensitive to the growing number of flows. It also achieves a lower tail latency.
