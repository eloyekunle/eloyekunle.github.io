---
layout: post
title: 'Notes on "THEMIS: Fair and Efficient GPU Cluster Scheduling"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf>

**Problem**

An increasing number of organizations are incorporating machine learning (ML) models into their products in order to unlock new business opportunities. However, ML models training can be time- and resource-intensive, with jobs executing in parallel across several GPUs. Due to the management overhead and cost of running GPU clusters, it is much more efficient for organizations to consolidate all GPU resources onto a single shared cluster. While this can lead to a more efficient setup, this does not necessarily lead to fairness in sharing these GPU resources.

Cluster users want their tasks to finish as fast as possible, and the authors describe this requirement as *sharing incentive (SI)*. SI implies that if there are N users sharing a cluster C, then each user’s performance should be no worse than using a private cluster of size *C/N*. If SI is absent, users sacrifice performance and will prefer to deploy their own private clusters. The authors show that existing state-of-the-art schedulers (e.g. DRF, Quincy) violate this key requirement because they use techniques designed for big data workloads and ignore unique characteristics of ML workloads such as their *long durations* and *placement sensitivity*.

**Approach**

In addition to SI, the authors show that ignoring placement sensitivity affects the Pareto Efficiency (PE) and Envy Freeness (EF) properties. They introduce a new metric called Finish Time Fairness, which is a ratio of an application's independent finish-time to its shared finish-time. Sharing incentive is attained when this ratio is at most 1\. An application’s finish-time fairness is a function of the GPU allocation that it receives.

To perform these allocations, the authors propose a multi-round partial allocation algorithm with the strategy proofness (SP) property which also satisfies PE and EF properties. 

At the beginning of a round (*visibility phase*), the *arbiter* requests apps for their finish-time fairness metrics estimate, and then selects a (tunable) fraction of the active apps with the greatest estimates, which are also the apps at risk of not meeting SI. Each app scheduler contains an *agent* which submits *bids* to the arbiter that reflects their new finish-time fairness metric from acquiring different GPU subsets. 

Then (*allocation phase*), the arbiter picks the winning bids based on the partial allocation algorithm and leftover allocation scheme and notifies the agents which in turn notifies the ML app scheduler which then allocates the GPU resources among the constituent jobs.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper provides a very detailed evaluation of the performance of THEMIS across different kinds of workloads and shows that it increases fairness and efficiency compared to other schedulers, even under high cluster contention. However, the performance and resource usage of the Agent and Arbiter as cluster size grows are not evaluated. Since the arbiter evaluates bids from the different ML app scheduler agents, it will be helpful to see how the system itself scales, especially since the resources consumed by the system are resources that cannot be offered to customers for additional revenue. Evaluating this can also serve as a good follow-up idea.
