---
layout: post
title: 'Notes on "Sonata: Query-Driven Streaming Network Telemetry"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://www.cs.princeton.edu/~jrex/papers/sonata.pdf>

**Problem**

Existing telemetry systems do not allow operators to express queries to perform complex analytics on network traffic data, or scale to large traffic volumes and rates. Some of them can collect and analyze the traffic data in real-time but have limited query expressiveness, while some incur high processing and storage costs that don’t scale to high traffic rates and queries.

Existing telemetry systems either rely on programmable switches or stream processors, and this usually determines whether they are trading off scalability for expressiveness or vice versa. This is because programmable switches can scale to high data rates but the queries they can support are limited by the capabilities and memory in the data plane. On the other hand, stream processors can express more complex queries but cannot scale to high data rates.

**Approach**

The authors introduce Sonata (Streaming Network Traffic Analysis) which is a query-driven network telemetry system. The key idea is that programmable switches and stream processors share a common processing model, which is applying an ordered set of transformations over structured data in a pipeline. What Sonata tries to do is to combine the strengths of both technologies into a single system, and thus is able to operate at line rate for high traffic volumes and rates, while still supporting expressive queries.

Sonata provides a declarative interface that can express queries for a wide range of telemetry tasks. To enable scalable and real-time execution, Sonata partitions each query across the programmable switches and stream processors, while trying to run as much of the query as possible on the programmable switches at line rates. This helps to reduce the load on the stream processor.

Since a lot of queries typically try to find “needles in a haystack”, Sonata implements dynamic query refinement based on the query and workload in an attempt to reduce the stream processor workload. Sonata uses historical packet traces to refine the input queries dynamically. To do this, Sonata modifies the input queries to start at a coarser level of granularity than requested, and then subsequently chooses finer granularities that reduce the load on the stream processor. However, this can introduce additional delay in detecting the traffic, but this is an acceptable tradeoff.

Evaluation of Sonata reveals that it is able to express different kinds of queries while using fewer lines of code than P4, and about the same as Spark. The performance evaluation also shows that it significantly reduces the workload on the stream processors for single-query and multiple-query scenarios.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

Sonata is able to transparently execute data analytics queries and dynamically adjust execution parameters where possible, without requiring operator awareness of how/where the query is eventually processed. This makes it a very powerful way to execute these queries. Sonata dynamically refines input queries based on historical packet traces and a further evaluation into how accurate these work for different kinds of queries, and their performance can be a good follow-up idea. It might also be helpful to provide Sonata libraries that can be integrated into existing programming languages as libraries, to improve ease of adoption.
