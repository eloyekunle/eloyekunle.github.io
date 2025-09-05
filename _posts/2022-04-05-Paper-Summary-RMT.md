---
layout: post
title: 'Notes on "Forwarding Metamorphosis: Fast Programmable Match-Action Processing in Hardware for SDN" (RMT)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://people.cs.rutgers.edu/~sn624/552-F20/papers/rmt.pdf>

**Problem**

Software-Defined Networking (SDN) physically separates the roles of the control and forwarding planes in a switch and provides an open interface between them. Providing programmatic control to the forwarding plane allows network operators to add new functionality to their network.

OpenFlow is an open interface that allows the control plane to program the forwarding plane, and is based on an approach known as “Match-Action”. A subset of the packet bytes is matched against a table that specifies the corresponding action to be performed on a particular matched entry.

Existing match-action hardware is not easily reconfigurable, so supporting new features frequently requires replacing the hardware. If we could have more flexibility in the hardware, then we could support new types of packet processing at run-time. However, there is a natural trade-off between programmability and speed, so more flexibility may come at the cost of speed.

**Approach**

The simplest approach to match-table processing is a Single Match Table (SMT) model where sets of packet header fields are matched against entries in a single match table. An improvement on this model is the Multiple Match Table (MMT) which allows smaller match tables to be matched by a subset of packet fields.

The authors explore how to achieve programmability without giving up performance by using a Match-Table model known as the Reconfigurable Match-Action Table Model (RMT) which is a refinement of the MMT. The four key improvements that RMT makes over MMT for data planes are:

- Enabling field definitions to be altered and new fields added,  
- The number, topology, widths, and depths of match tables can be specified,  
- Ability to define new actions, and  
- Arbitrarily modified packets can be placed in specified queues for output at any subset of ports with a queueing disciple specified for each queue.

The hardware architecture is a 640 Gb/s switch chip that has an aggregate throughput of 960M packets/s. The design provides 32 physical match stages at both ingress and egress, 16 parsers, and 1 egress and ingress pipeline.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper is well-structured and goes from high-level ideas down to very low-level details in different places. It combines ideas from networking, hardware design, algorithms, and compilers and applies these ideas to switch programmability. However, this makes some parts of the paper hard to read.

In RMT, each pipeline stage can only access local memory, so RMT must allocate memory for a table in the same match+action stage. This conflates memory allocation with match/action processing, which makes table placement challenging and can result in poor resource utilization. Also, the serial pipelined execution order can lead to the under-utilization of hardware resources for programs where matches and actions are imbalanced. 

These two problems are addressed in dRMT (S. Chole et al, 2017\) whose key idea is to disaggregate the hardware resources of a programmable switch. dRMT separates table memory from the processing stages and also replaces the sequentially-wired pipeline stages with a set of match-action processors.
