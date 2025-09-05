---
layout: post
title: 'Notes on "Scaling Distributed Machine Learning with In-Network Aggregation" (SwitchML)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/nsdi21-sapio.pdf>

**Problem**

Machine learning solutions these days rely on sophisticated models which train on increasingly large data sets. To cope with the increased training time these models require, ML practitioners now use distributed training which makes use of large clusters consisting of terabytes in storage, hundreds of nodes equipped with hardware accelerators, and superfast networking connecting them. 

Training jobs now make use of dozens to hundreds of workers which may even be globally distributed due to edge-based data collection and processing, and training alternates between *computation* phases where each node runs algorithms on locally-present data, and *communication* phases where nodes synchronize the models by sending and receiving updates to each other. This alternation typically continues for the life of the ML model.

As computation speed has increased over the years, the communication phase now has an increasing impact on overall training time, bottlenecked by the network performance.

**Approach**

The authors of this paper try to alleviate the network bottleneck by placing an aggregation primitive in the network to accelerate distributed ML workloads. During synchronization phases, aggregating the amount of data that needs to be transmitted can help to increase throughput and speed up the training time. Programmable switches are able to perform integer aggregation but due to their limited computation power, the authors implement a combined switch-host architecture where end-hosts are responsible for managing reliability and performing more complex computations.

The authors decouple a simple aggregation operation into an addition phase (on switch) and a division phase (on end-host due to efficiency). Since addition is commutative and associate, the order of packet arrivals at the switch does not matter while still preserving correctness.

Each arriving packet carries a pool index that identifies a particular aggregator to be used, and a vector of integers to be aggregated. Once all workers have sent vectors for the same pool, the switch sends the result to the workers who are then able to deterministically reuse the pool index for a new set of vectors.

This mechanism helps implement a simplistic form of flow control since if all workers have not received results from a previous step, the next step cannot finish processing. A packet loss recovery mechanism is also built on this by keeping track of one older version and the current version of results on the switch to facilitate retransmissions.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

This paper attempts to optimize the communication phase of distributed machine learning algorithms in *rack-scale architectures*. It is well-presented and easy to read, with helpful algorithm pseudocode to aid understanding. The authors suggest scaling beyond a rack by aggregating on multiple levels; the implications of this on model accuracy were not evaluated. Accuracy could degrade after one or two aggregation steps. Evaluating this is a possible follow-up idea.

There’s a lot of research focused on such optimizations, such as Gaia (Hsieh et al, 2017\) which focuses on optimizing communication across *globally distributed* data centers but uses the *parameter server* architecture. Other approaches include Google’s Federated Learning which brings model training to the network edge and only sends aggregated updates to the cloud. Cartel (Daga et al, 2019\) also improves ML model communication in geo-distributed data centers.
