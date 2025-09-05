---
layout: post
title: 'Notes on "Network Requirements for Resource Disaggregation"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/conference/osdi16/osdi16-gao.pdf>

**Problem**

Existing data centers are built around servers, each of which has a tight integration of all resources such as CPU, memory, and storage. This has been the prevailing design for decades but has been exhibiting some challenges recently. Some of these challenges include inefficient resource utilization, coarse failure domain (failure of a single hardware component brings down the whole server), poor hardware elasticity, and poor support for heterogeneity.

Recent research has been focusing on data center disaggregation, which is a concept that involves breaking monolithic servers into independent, failure-isolated network-attached “blades”, which are all attached to a network and communicate over the network. This can make it easier for each resource type to evolve independently, and also enables efficient provisioning and scheduling of resources across jobs.

One of the key enabling factors for disaggregation has been advances in network technology, and this will continue to be an enabling or blocking factor in its increasing adoption. This is because, in order to maintain application-level performance, the network fabric must support all the new inter-resource communication which used to be contained within a single server, but are now over the network fabric.

**Approach**

The focus of this research is investigating the minimum requirements that the network in disaggregated data centers must provide in order to maintain application-level performance, compared to server-centric architectures. The minimum network requirements were evaluated in the context of ten workloads across some popular open-source systems \- Hadoop, Spark, Memcached, HERD, SparkSQL, Spark Streaming, GraphLab, and Timely dataflow. The network metrics evaluated were *bandwidth* and *latency* requirements.

The researchers emulated remote memory access by partitioning memory into “local” and “remote” portions, and one of the key evaluations was determining the performance impact of varying the size of the “local” memory. Upon evaluating application performance, they split the applications into two categories: “Class-A” applications can maintain an average performance penalty under 5% with a network end-to-end latency of 5µs and bandwidth of 40Gbps, while “Class-B” applications require latencies of 3µs and 40-100Gbps bandwidth to remain under 8% performance penalty. Also, Class B applications are more sensitive to an increase in local memory, though increasing beyond 40% has almost no impact on performance.

They also found that the primary latency bottleneck is due to network software and not hardware. Latency introduced by the endpoint is about 66% of the inter-rack latency and roughly 81% of the intra-rack latency. Therefore, there is not a whole lot of performance gains to be obtained from optimizing switch hardware. Work on bypassing the kernel for packet processing and NIC integration can make a bigger impact on latency performance.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

This is a well-written and well-structured paper that evaluates minimum network bandwidth and latency requirements for disaggregated data centers. Since the paper’s publication, there has been new networking hardware in use in data centers that have latency and bandwidth optimizations. A follow-up to the research would be to evaluate the minimum requirements in an actual modern data center and see if they meet the necessary minimum latency targets.
