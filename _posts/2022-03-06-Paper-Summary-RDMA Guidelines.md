---
layout: post
title: 'Notes on "Design Guidelines for High Performance RDMA Systems"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/conference/atc16/atc16_paper-kalia.pdf>

**Problem**

Despite the increasing popularity and lowering cost of modern high-performance RDMA-capable networks, software designers still find it challenging to use their advanced capabilities to best effect. The main challenge is that there are lots of options for using the NIC, and the differences in NIC architecture mean that there are several complex low-level factors that can affect the performance of these operations across different NICs.

It is also challenging to adequately match an application’s performance requirements to the RDMA capabilities because different choices in RDMA options can result in wildly different performance implications across different RDMA designs. Therefore, it is important to evaluate and optimize the most important factors that affect application performance when using RDMA NICs. 

This research aims to help system designers address this problem by providing guidelines to evaluate and optimize these factors. The second aim of the research is to evaluate these proposed guidelines to real systems across different generations of RDMA hardware and compare their performance. For this, they created models of RDMA operations and their associated CPU and PCIe costs, and also a set of open-source software for measurement and analysis.

**Approach**

The authors presented five RDMA design guidelines.

- Reduce CPU-initiated MMIOs: DMAs are more CPU- and bandwidth-efficient. Therefore, applications can take advantage of this to batch messages where possible to reduce MMIO. Also, reducing the number of cache lines used by a WQE can improve throughput.  
- Reducing NIC-initiated DMAs can save NIC processing power and PCIe bandwidth. Inlining RECV payload, and sending header-only SENDs and RECVs can help with this.  
- Using multiple Queue Pairs (QP) per CPU core (NIC parallelism) can increase CPU efficiency.  
- Avoiding contention among NIC PUs can improve performance, and this is relevant to the use of atomic operations because NICs use internal concurrency controls for concurrency.  
- Avoiding NIC cache misses is critical because cache misses will require a read over PCIe. PCIe counters were used to detect and measure the cache misses.

To evaluate these guidelines, the authors used them to improve two systems \- networked sequencers and key-value stores.

Optimizations on the sequencer include batching, 3 QPs per core, and header-only SENDs. Compared to a sequencer that uses atomic fetch-and-add over RDMA, these optimizations provide a 50x performance improvement. Applying batching to the HERD key-value store leads to an 83% improvement in the per-core throughput, and also improvements in the peak throughput. When RDMA atomics are used for the KV-store, this results in low throughput for PUT requests due to locking inside the NIC.

Finally, the authors present a set of low-level factors affecting RDMA performance and present performance measurements, experiments, and models based on three common use cases \- batched operations, non-batched operations, and atomic operations.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

This paper presents a starting point for system designers to begin evaluating available RDMA hardware for use with their high-performance systems and applications, with the additional help of the accompanying open-source benchmarking utilities. However, there is poor portability of applications across different RDMA hardware because what improves performance on one NIC architecture will not necessarily do so on another.
