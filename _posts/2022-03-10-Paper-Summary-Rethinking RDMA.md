---
layout: post
title: 'Notes on "PRISM: Rethinking the RDMA Interface for Distributed Systems"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://irenezhang.net/papers/prism-sosp21.pdf>

**Problem**

RDMA is now an important tool to achieve high throughput and reduce latency in modern data centers. However, some applications are unable to efficiently express their distributed protocols with the interfaces provided by RDMA. The efficiency and better performance provided by RDMA quickly disappear when applications need to make extra network round trips or bring in the CPU for some stage of the computation. In such cases, it would simply be better not to bypass the CPU at all.

Examples of distributed systems functionality that need to be supported are: navigating data structures (requires multiple RDMA reads), supporting out-of-place writes (challenging due to multiple concurrent reads), optimistic concurrency control (updates require synchronization), and chaining operations, which involves performing compound operations where subsequent operations depend on previous ones.

**Approach**

The authors of this paper believe that the best way to support today’s distributed systems functionalities is to extend the basic RDMA interface with additional primitives.

For this, they propose PRISM (Primitives for Remote Interaction with System Memory) which was created to adhere to three core principles: (i) generality: The interfaces should not include any application-specific functionality. This will also make it possible to express a wide domain of applications using a small set of primitives, (ii) minimal interface complexity, and (iii) minimal implementation complexity.

PRISM adds four new features to the RDMA interface:

1. Indirection: RDMA applications frequently need to traverse remote data structures, which involves following pointers. PRISM allows READ, WRITE, and compare-and-swap (CAS) operations to take indirect arguments which allows an address to be interpreted as the address of a pointer to the actual target.  
2. Memory Allocation: A server-side process can register a queue of buffers with the NIC. Upon an ALLOCATE request, the NIC pops a buffer from this free list, writes the provided data into it, and returns the address. Used with operation chaining, this can be a quite useful feature.  
3. Enhanced compare-and-swap: The existing CAS operations provided by the RDMA standard is insufficient to implement performant applications and thus are rarely used in practice. PRISM extends the atomics interface and incorporates indirect addressing as described above, and also supports arithmetic operators in the compare phase.  
4. Operation chaining: Distributed applications frequently need to perform sequences of data-dependent operations. PRISM enables conditional operations, which delays the execution of an operation until previous operations are completed successfully. It also supports output redirection which allows the output of an operation to be written to memory instead of returned to the client.

In order to demonstrate the wide applicability of the new APIs, the authors enhance three common distributed applications with these new primitives: a key-value store, a replicated block store, and a transactional key-value store. These implementations showed latency and throughput improvements compared to the baseline.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

This work presents what should be the next step in the evolution of RDMA APIs by trying to make it much easier to take advantage of RDMA’s capabilities. While the software implementation provides a convincing argument, it would be great to see hardware manufacturers implement some of these primitives in their interfaces to improve adoption.
