---
layout: post
title: 'Notes on "PipeDream: Generalized Pipeline Parallelism for DNN Training"'
category: Blog
tags: [papers, pipedream]
---

**Link to Paper:** <https://people.eecs.berkeley.edu/~matei/papers/2019/sosp_pipedream.pdf>

Pipedream is a system that helps to improve the performance of Deep Neural Networks (DNN) training by reducing communication overhead among worker nodes.

### Problem

Traditional DNN training requires nodes to have all-to-all communications at several points during training, which can lead to significant underutilization of GPU resources if nodes spend most of their time waiting on communication. As GPU performance improves, more and more of the DNN training time will be spent waiting on communication which is undesirable.

DNN training involves multiple iterations of forward and backward passes. While there have been many attempts to introduce parallelism in DNN training, most have involved parallelizing a single iteration across a set of workers in an approach the paper refers to as “intra-batch parallelization”.

### Approach

Pipedream introduces a new approach to DNN training called “pipeline parallelism” which combines “intra-batch parallelism” with “inter-batch parallelism”. This involves partitioning the layers of a DNN model into multiple stages, and each stage is mapped to a separate GPU that performs the forward and backward pass for all layers in that stage. This can greatly reduce inter-worker communication, achieving high hardware efficiency with no pipeline stalls in steady state.

Previous works have attempted pipeline parallelism, but Pipedream addresses some challenges which make this approach scale to large real-world models including an efficient partitioning algorithm that makes an optimal decision based on short profiling run before initializing training; efficient scheduling based on a round-robin load-balancer; and weight stashing to avoid a mismatch between the versions of weights used in the forward and backward passes.

### Strengths and Weaknesses

The paper was very well-written, although needing a background in the existing literature of machine learning research which made it a bit difficult to follow in some areas.

The background section made it very easy to understand the historical approaches to DNN training, and provided a good survey of work in this area. I also loved the evaluation section, because it analyzed Pipedream’s performance across a wide array of system configurations and for different kinds of machine learning workloads such as image classification, translation, language modelling and video captioning. The performance was also compared with that of existing approaches to DNN parallelization and provides a convincing argument for the performance of Pipedream.

### Lessons for Datacenter Systems

A related paper is Gaia (Hsieh et al, NSDI ‘17) which enables fast geo-distributed machine learning approaching LAN speeds while guaranteeing the correctness of the machine learning algorithms. This was done by eliminating insignificant model updates between data centres after an analysis revealed that most communication among nodes while updating a machine learning model does not contain significant information and can be filtered out.

With increasing applications of machine learning in different domains and the huge amounts of data that these systems typically need, communication costs can easily become a bottleneck. 

State-of-the-art network systems and GPUs can definitely help, but one quickly reaches a point of diminishing returns from improving hardware configurations. Application developers need to pay increased attention so that their applications make efficient use of the available hardware to improve performance.
