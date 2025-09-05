---
layout: post
title: 'Notes on "LegoOS: A Disseminated, Distributed OS for Hardware Resource Disaggregation"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/osdi18-shan.pdf>

**Problem**

This research proposes a new operating system built specifically to manage disaggregated systems. Hardware resource disaggregation involves breaking monolithic servers into independent, failure-isolated network-attached components. Disaggregated systems hope to solve many problems, such as inefficient resource utilization, coarse failure domain (failure of a single hardware component brings down the whole server), poor hardware elasticity, and poor support for heterogeneity.

However, existing operating systems are not well-suited for this architecture. Monolithic kernels, microkernels, and exokernels rely on a single-node view of the server, while multi-kernel solutions which view different server devices separately still run in a single server and do not manage distributed devices.

**Approach**

This research proposes a new OS architecture called *splitkernel* for resource disaggregation and then introduces an OS called *LegoOS* which implements this architecture.

Splitkernel breaks an OS into pieces (called *monitors*) of different functionalities, each of which manages a single hardware component. Each monitor is stateless and shares no or minimal states with other monitors. The components communicate with each other via message passing over a general-purpose network layer. The splitkernel also involves global managers which manage resource allocation and handle failures.

LegoOS is an OS that implements the splitkernel architecture. It exposes a distributed set of virtual nodes (or *vNode*) to users, each of which has a unique ID, virtual IP address, and its own storage mount point. Multiple users can run in a vNode and each user can run multiple processes. Also, a single vNode can run on multiple hardware components, and a single hardware component can host resources for more than one vNode. However, this is internal to the OS and is not exposed to the LegoOS users. The initial implementation of LegoOS focuses on three types of hardware components: processor, memory, and storage, called *pComponent, mComponent,* and *sComponent* respectively.

LegoOS also supports Linux ABIs for backward compatibility, and this makes it easy to adopt the OS. Some modifications had to be made to do this because Linux processes are stateful, while LegoOS has stateless components.

To improve performance, LegoOS adds an extra layer of cache in the pComponent called the ExCache in order to exploit temporal locality in memory access and reduce memory access to frequently accessed sections of memory.

**Strengths, Weaknesses & Possible Follow-Up Ideas**

The paper clearly explains the challenges with existing OS designs and separates the architecture from the OS implementation. This makes it possible to design other OSes which can implement the architecture, or extend LegoOS with support for other hardware components. The backward compatibility with Linux ABIs is a very good aspect of the implementation that can make it easy to evaluate the OS on existing applications and improve adoption.

Performance evaluation shows a slight decrease in performance for LegoOS in running actual applications, but better resource utilization and improved failure handling. This presents a cost-performance trade-off for data centers. However, as network speed improves, this may change.

An evaluation of the OS in an actual data center, running live load will help to better understand the performance hit this OS incurs, and evaluate application-dependent optimizations which may improve it. This can be followed by monitor implementations for recent specialized hardware e.g. TPUs.
