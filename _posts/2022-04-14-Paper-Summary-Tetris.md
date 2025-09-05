---
layout: post
title: 'Notes on "Multi-Resource Packing for Cluster Schedulers" (Tetris)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.cs.cmu.edu/~xia/resources/Documents/grandl_sigcomm14.pdf>

**Problem**

Modern data analysis makes use of large clusters which execute *jobs* consisting of several *tasks*. There can be different kinds of jobs running in a cluster at a time, each of them having varying resource requirements along CPU, memory, disk, and network. Tasks can be constrained to just one or multiple resource types at a time. 

When scheduling tasks in a cluster, a scheduler must consider the task’s requirements on all the resources in order to maximize the task throughput and speed up job completion. If a task is given a lot of one resource but less of another, then it takes longer to finish and this is inadequate.

The authors show that existing schedulers have a limited ability to pack tasks because they typically consider just one of the CPU and memory resources, ignoring network and disk requirements. They also typically allocate these resources based only on fairness. They show that this can lead to resource *over-allocation* or *fragmentation* which delays job completion and increase makespan.

**Approach**

The problem of efficiently allocating multiple resources to tasks is similar to the multi-dimensional bin packing problem, but with some important differences such as the need to accommodate varying task requirements, task elasticity (tasks can function with less than peak demand), online arrival of jobs in the cluster, dependencies between tasks, and other cluster activity such as evacuation and ingestion of new data.

The authors present Tetris, a cluster scheduler that aims to solve this problem, and which packs tasks to machines based on their requirements along multiple resources. Tetris adaptively learns task requirements and monitors available cluster resources and uses a packing heuristic to select a task to machine allocation that improves makespan. Tetris also uses a multi-resource version of the *smallest remaining time* (SRTF) to reduce the average job completion time.

The authors show that prior work on *pareto-efficient* and *work conserving* fair allocations do not necessarily yield the best completion time and makespan. They demonstrate that with a little unfairness, much better performance can be achieved in resource schedulers and expose this trade-off with a knob.

Tetris is incorporated into the Yarn framework in Hadoop. It estimates task demands from previous executions of the job and previously completed tasks in the job, and also uses resource trackers to monitor available resources at each node. Performance evaluations show that Tetris improves makespan and job completion by 30% in deployment and up to 40% in simulations over Facebook traces.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper is well-written, with detailed discussions of the background and problem to be solved which aids understanding of the paper. 

Tetris runs a resource tracker process on each node which then reports to a central resource manager that handles scheduling. This leads to a central point of failure and possible performance bottleneck for the entire system. Evaluating how this part of the system scales with increasing cluster size is a possible follow-up idea. Also, while this estimation can work well for most tasks, it may not work so well for tasks with varying resource demands and usage over the course of their execution.
