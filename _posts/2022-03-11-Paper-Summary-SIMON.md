---
layout: post
title: 'Notes on "SIMON: A Simple and Scalable Method for Sensing, Inference and Measurement in Data Center Networks"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/nsdi19-geng.pdf>

**Problem**

In order to improve network performance and easily debug problems in distributed applications and networks, it is important to take network measurements and implement monitoring. However, monitoring networks at scale and at near real-time speeds is still very challenging to do.

There are two main approaches to network telemetry \- switch-based and end-host-based.

In switch-based telemetry, measurements are captured at each switch and they can be accurate or approximate. Approximate measurements only provide an approximate count of packets/bytes passing through one switch, and they require extra bandwidth to move the measurements to the edge where they undergo a lot of extra processing to generate the network-wide views. Accurate measurements generate a lot of data, which can limit its scalability. Also, it requires each node to be able to perform in-band network telemetry which isn’t always the case. One general downside of switch-based telemetry is the difficulty in matching network performance to application performance since measurements are taken at the switches.

In edge-based telemetry, events are recorded at the end-hosts. From these, attempts are made to infer the internal network state. One approach to edge-based is known as *network tomography*, which attempts to determine internal network state quantities such as queue delays and backlogs based on probes and data packets collected at the network edge. When deployed in wide-area networks, typical approaches to network tomography do not yield much accuracy due to a lack of knowledge of the underlying network topology, and end-to-end traversal times are much longer than queuing delay times.

**Approach**

Some factors can make network tomography if we restrict its use to data centers. The network topology is known to the operators, and the typical network topology of the Clos type provides multiple paths between nodes which makes packet wire times negligible in the overall end-to-end traversal time, which is instead dominated by queueing times. Therefore, without requiring any modifications to the existing switching infrastructure, it is possible to have accurate, scalable, and near real-time network measurements which can be easily matched to application performance. This is exactly what the researchers set out to do.

The authors introduce SIMON which uses a network tomography-based approach to reconstruct the full network state variables such as queuing times, link utilization, and queue and link compositions.

First, they introduce a signal processing framework to quantify an appropriate balance between the amount of data collected and accuracy, which they try to keep at 97.5%. Their analysis suggested that optimal reconstruction intervals vary inversely with link speeds.

SIMON uses a mesh of probes to obtain information about the queue sizes and wait times. Probe packets were chosen over data packets because data packets do not yield an accurate network queue reconstruction across multiple queues. To speed up SIMON, they leverage the hierarchical structure of the network topologies, and also use neural networks. Both methods enable SMON to run in near real-time.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper has well-presented arguments and reasonings. Getting it actually deployed in data centers will still require some development work on the part of the DC operators since the code is not open-sourced. Making the data format compatible with existing visualization and analysis will be important to make adoption easier. A follow-up idea is also to explore possible actions that can be taken automatically based on the collected data, e.g. altering packet routes based on real-time network states.
