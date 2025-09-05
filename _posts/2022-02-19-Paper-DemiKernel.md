---
layout: post
title: 'Notes on "The Demikernel Datapath OS Architecture for Microsecond-scale Datacenter Systems"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://irenezhang.net/papers/demikernel-sosp21.pdf>

**Problem**

As IO speeds continue to outgrow CPU speeds, a new technique to address this technique has been developed, called kernel-bypass. This technique allows more direct access to devices by eliminating the OS kernel from the I/O datapath. The functionality typically in the OS kernel is moved to user-space or offloaded to the device itself. This provides several latency-related advantages, such as lower user to kernel-space transitions and zero-copy IO.

However, as this technique is growing in popularity, there are still challenges limiting its adoption. New devices features are developed every year, and engineering resources are needed to port existing applications to different devices and keep re-designing them to keep pace with these changes. Also, data centers are increasingly heterogeneous and deploy many generations of hardware at the same time.

**Approach**

DemiKernel aims to solve this problem by developing a new OS API and architecture for heterogenous kernel-bypass devices, and several library OSes that implement this design. The three main design goals of DemiKernel are to simplify system development of µs-scale systems, offer portability across heterogeneous devices, and achieve nanosecond-scale latency overheads.

To achieve these design goals, DemiKernel implements two concepts. 

The first is a portable datapath API known as PDPIX, which extends the standard POSIX API to support the needs of µs-scale kernel-bypass I/O. PDPIX is queue-oriented, not file-oriented (as in POSIX) so system calls that would return a file descriptor in POSIX return queue descriptors in PDPIX. PDPIX is also designed around asynchronous IO operations, and API calls return a *qtoken* which indicates their asynchronous results. Applications can use the *wait\_\** library calls to fetch completion events attached to the qtoken.

The second concept is a set of library operating systems that implement this API. These libOSes are available on Linux (*DemiLin*) and Windows (*DemiWin*) operating systems, and each one supports a specific kernel-bypass device. The IO devices currently supported are RDMA, SPDK, and DPDK. This allows an OS to offer portability across the different devices on either Linux or Windows since each device is usually very different from the other.

The three main components for the libOS implementations are I/O processing, memory management, and a coroutine scheduler. The IO stacks minimize latency by polling, using a *fast-path I/O coroutine*. There are two other coroutine types \- *background coroutines* are used for other I/O stack work, and an *application coroutine* runs an application worker to process a single request.

Evaluation of DemiKernel demonstrates that it portably achieves ns-scale I/O processing, run-to-completion, and zero-copy for networking and storage while being portable across different platforms and I/O devices.

**Strengths, Weaknesses & Possible Follow-Up Ideas**

The paper is well-structured and easy to read. It tries to propose a solution for a current issue in data centers, which is likely to be more challenging in the future. The ease of portability of existing applications is a major strength of the OS which will enable faster adoption. There is likely to be future research exploring improvements in memory management, scheduling, etc in Demikernel or possibly as alternative datapath OSes, and also evaluations in real data center deployments.
