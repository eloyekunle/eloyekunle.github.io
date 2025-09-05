---
layout: post
title: 'Notes on "Inside the Social Network’s (Datacenter) Network"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://conferences.sigcomm.org/sigcomm/2015/pdf/papers/p123.pdf>

This paper analyzes network traffic patterns in Facebook’s data centers. 

**Facebook’s Datacenter Architecture**

Facebook’s data centers consist of multiple data center sites and a backbone connecting these sites. Each site contains multiple data centers, and each data center contains multiple clusters. Machines are organized into racks and connected to a top-of-rack switch (RSW), each of which is connected to 4 cluster switches (CSW), which are connected to each other via Fat Cat (FC).

Data was collected primarily via two methods: *Fbflow* and Port Mirroring. *Fbflow* is a monitoring system that samples packet headers from Facebook’s machine fleet. Port mirroring is used to collect high-fidelity data over short intervals by deploying trace collection machines in racks hosting different services \- Web, Hadoop, Cache, and MF nodes.

**Results**

From the observed traffic, Hadoop is the only server out of the four which exhibits rack-local traffic. Most of the Web server traffics stays within the cluster (intra-cluster). Cache followers are usually responding to web servers so they have high intra-cluster traffic, while cache leaders are mostly maintaining coherency across clusters and backing databases so they engage primarily in intra- and inter-datacenter traffic. Apart from Hadoop, the traffic patterns for the other server classes are more stable. Based on previous analyses of datacenter network traffic patterns, we would expect 50-80% of traffic to be rack local but the results here show the opposite.

Also, while previously published data finds that demand is frequently concentrated and bursty, Facebook’s network traffic is widespread, uniform, and stable with rapidly changing, internally bursty heavy hitters.

The analysis also finds that outside of Hadoop, packets are small and do not exhibit on/off behavior. Servers communicate with 100s of hosts and racks concurrently but the majority of traffic is often destined to 10s of racks. This also challenges previously published data.

**Lessons for Datacenter Design**

Designing network fabrics that interconnect and manage the traffic within data centers requires an understanding of the demand that will be placed on the networks by end hosts. While there existed previous analyses of data center network traffic patterns, most had focused on Microsoft’s data centers because of the reluctance of data center operators to disclose their network traffic information.

Analyzing Facebook’s network traffic behavior provides a new perspective in evaluating proposals and assumptions about data center traffic.

**Strengths and Weaknesses**

The authors convincingly argued that existing data center network traffic patterns would not necessarily apply to all kinds of data centers and presented reasonable explanations for the patterns seen in Facebook’s network traffic.

However, details about data collection and processing are not fully revealed and inconsistency of some results lack explanation.

**Possible Follow-Up Ideas**

Analysis of other large-scale data centers will also be very helpful to compare with the findings in this paper because they may present different traffic patterns. 

The paper also mentions Facebook’s new data center architecture called Fabric, and it will be very interesting to see if the findings in this paper carry over to the new system and if not, to analyze the changes.
