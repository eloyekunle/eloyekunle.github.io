---
layout: post
title: 'Notes on "Datacenter RPCs can be General and Fast"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/nsdi19-kalia.pdf>

**Problem**

General approaches to achieving high performance in datacenter networking software have been about sacrificing generality. As a result, there have been several specialized distributed systems designed for niche networking technologies such as RDMA, FPGAs, and programmable network switches.

eRPC shows that in many cases, we can attain high performance without taking on any extra complexity. The previous belief was that you can either have general-purpose networking that works in many cases but is not very fast, or you can optimize at lower-level network interfaces in order to obtain high performance and sacrifice the generality.

**Approach**

eRPC is a general-purpose RPC library that achieves performance comparable to specialized systems, even while running on commodity CPUs in traditional datacenter networks. eRPC achieves a high message rate, high bandwidth for large messages, and scalability with low latency to large clusters with lossy Ethernet.

The main secret is a design that carefully optimizes for the common case and traffic patterns. This common case consists of small messages, short-duration RPC handlers, and congestion-free networks.

In eRPC, RPCs execute at most once and are asynchronous. RPC servers register request handler functions with unique request types, and the clients include the request types when issuing RPCs, and they receive continuation callbacks on RPC completion.

One key design decision in RPC systems is which thread runs an RPC handler. eRPC supports running request handlers in both dispatch threads for short duration request types, and worker threads for long-running requests. The programmer specifies which mode the handler should be run in while creating the dispatch threads.

eRPC chooses packet I/O instead of RDMA writes to send messages. This helps avoid scalability bottlenecks in RDMA’s use of circular buffers. Also, eRPC can replace RDMA’s NIC-cached connection states and manage connection states in the CPU instead. eRPC also uses zero-packet I/O to provide performance comparable to lower-level interfaces such as RDMA and DPDK. eRPC also supports concurrent requests per session which can complete out-of-order with respect to each other. Session credits support end-to-end flow control to reduce switch queueing.

eRPC also has a wire protocol that is optimized for small RPCs and accounts for per-session credit limits. Rate limiting and wire protocol state are moved to the client, freeing the server CPU from this overhead and removing the need for coordination on a rollback. For congestion control, eRPC includes mechanisms to easily implement Timely or DCQCN, used by Google and Microsoft respectively.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper effectively demonstrates that data center operators and application developers can achieve higher performance without extra hardware complexity. This is an important paper in RPC design that builds on the authors’ previous experience working on FaSST. The design was tested with real applications and also released as open-source software.

One of the barriers to the deployment of the research into fast networking software such as eRPC is that they assume bare-metal access. However, most applications running in data centers run on cloud virtual machines. If this problem is addressed, then the barrier to entry for developers can be minimized.
