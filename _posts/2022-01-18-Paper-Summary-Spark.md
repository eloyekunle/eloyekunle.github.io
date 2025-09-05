---
layout: post
title: 'Notes on "Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing" (Spark)'
category: Blog
tags: [papers, spark]
---

**Link to Paper:** <https://www.usenix.org/system/files/conference/nsdi12/nsdi12-final138.pdf>

Spark is a framework used for distributed processing, developed by a research team at UC Berkeley. It provides faster analytics for different workload types and supports multiple languages.

One of the problems Spark was developed to solve is the I/O overhead incurred by existing frameworks such as MapReduce, which led to the development of in-memory data sharing. The benefits of in-memory sharing are:

- DRAM access is significantly faster than HDD or SSD accesses.

- Serialization is unnecessary since data is already in memory. For example, processing a job via MapReduce would have multiple accesses to storage while writing and reading intermediate results which create additional overhead.

Spark introduces a concept of Resilient Distributed Datasets (RDD), which are read-only, partitioned collections of records. RDDs are created via deterministic operations on either data in stable storage or other RDDs. Each RDD has information about its lineage so it can be recomputed from data in stable storage. Users are also able to control the persistence and partitioning of these RDDs.

In order to track the lineage of RDDs across different transformations, the paper presents a graph-based representation for RDDs and classifies dependencies between RDDs into *narrow* and *wide* dependencies. Narrow dependencies allow for pipelined execution on a single node, and also more efficient recovery after node failure, unlike wide dependencies which require a shuffling of data across nodes.

This representation of RDDs is used in scheduling decisions for Spark. Each stage of the execution of an action is done with as many narrow dependencies as possible in order to achieve parallelism and reduce I/O contention. Tasks are also assigned to machines based on data locality.

Spark also facilitates interactive data mining by providing a console similar to that provided by programming language environments such as Python and allowing users to lazily execute operations on this data. Because the data is kept in memory during the entire run, responses to interactive operations can be provided more than an order of magnitude faster than working with on-disk data.

Upon comparison, Spark was shown to significantly outperform Hadoop for a variety of applications such as machine learning algorithms which typically require multiple rounds of iterations before convergence, and thus benefit from data being kept in memory during the run.

What I like about Spark is that it reduces the storage I/O for large data processing tasks, reading data as low as once. It also provides users with good control over the locality with reasonable defaults.  Another thing I love is the expressibility of the framework, which can express different kinds of existing data processing models such as MapReduce, SQL etc. I also love Spark’s interactive system which really cuts down the time needed to gain insights on huge amounts of data.

However, the in-built recovery mechanism may lead to greater recovery time when trying to compute a dataset from the lineage information available. Spark has in-built optimizations to reduce the recovery time, but it could still take longer than what is obtainable from existing frameworks.
