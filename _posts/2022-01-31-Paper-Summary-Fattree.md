---
layout: post
title: 'Notes on "A Scalable, Commodity Data Center Network Architecture" (Fat-tree)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.cs.yale.edu/homes/yu-minlan/teach/csci599-fall12/papers/fattree.pdf>

**Problem**

Data centers have been experiencing a massive increase in size in recent years. Institutions and companies now have clusters consisting of several tens of thousands of nodes, the growth of which has also been helped by the rise in commodity PC servers. Due to the nature of applications frequently executed in these data centers, the nodes have a high degree of communication with each other.

Existing network topologies, even when using the highest-end IP switches and routers are frequently unable to completely utilize the aggregate bandwidth available at the edge of the network, while incurring a tremendous cost. This can limit the overall system performance and complicate application design.

**Approach**

The authors present a new data center communication architecture called Fat-tree that leverages commodity Ethernet switches and routers to deliver improved bandwidth utilization for large-scale clusters, with better scalability. Fat-tree can support large-scale networks with tens of thousands of physical servers.

The network is modeled as a fat-tree topology, and routers are configured in 3 layers \- Core, Aggregation, and Edge. There are many equal-cost paths between adjacent layers, and the network eliminates the bandwidth bottleneck in the core layer.

The network includes a two-layer routing technique and two optional dynamic routing techniques which utilize flow classification with dynamic port-reassignment in pod switches. There is also a central flow scheduler with a global knowledge of the network state, and this achieves almost ideal bisection bandwidth for random communication mappings.

**Strengths and Weaknesses**

The paper is well-written and presented, with a clear formulation of the problem and proposed architecture. The backward compatibility with Ethernet, IP, and TCP is also a bonus for ease of deployment in existing data centers.

One weakness of the architecture is that to support non-blocking communication, Fat-tree requires a large number of switches in the core and aggregation layers and increased wiring requirements to interconnect them. This increases deployment cost, energy consumption, and management complexity. The authors present techniques to ameliorate the wiring complexity which may not adequately solve the problem.

**Possible Follow-Up Ideas**

One could explore possibly improving Fat-tree’s architecture to reduce the number of packet hops, which can be a significant overhead in large data center networks. Also, routing in Fat-tree incurs some overhead which may grow to dominate overall flow routing and scheduling time. A comparison of different network scheduling algorithms on Fat-tree’s architecture can be done, since different algorithms may prove more efficient for different kinds of network communications.

**Lessons for Data Center Design**

The paper explores the effect of network topology design on overall data center cost, network bandwidth utilization, application complexity, and heat dissipation in data centers. The authors showed that merely using higher-end switches and routers do not lead to significant benefit in network bandwidth utilization at the network edge relative to added cost.

Just as data centers increasingly make use of commercial-off-the-shelf compute and storage components to realize a better cost/performance ratio, this paper convincingly suggests that commodity switches can improve data center networks when architected correctly.
