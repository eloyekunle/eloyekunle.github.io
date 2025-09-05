---
layout: post
title: 'Notes on "High-Resolution Measurement of Data Center Microbursts"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://conferences.sigcomm.org/imc/2017/papers/imc17-final60.pdf>

**Problem**

As data centers have gotten bigger, they have incorporated network switches with significant bandwidth capacities. As a result of their speed, the networks operate on very small timescales, processing packets in nanoseconds with end-to-end latency rarely exceeding 1 ms. However, network monitoring systems used in data centers have not lived up to this new reality because they either operate on the scale of minutes or are heavily sampled. Systems which rely on these metrics for flow and congestion control are therefore incapable of responding to network bursts and congestion events that are too short-lived to be characterized by existing datasets.

**Approach and Findings**

The primary contribution of the paper was to develop a custom framework for high-resolution counter collection which was then integrated with Facebook’s in-house switch platform. Using this framework, the authors performed a data-driven analysis of three sets of counters:

- Byte count \- Total amount of bytes sent/received per switch port (Polled every 25 µs).  
- Packet size \- Packet sizes sent/received at each switch port (Polled every 25 µs).  
- Peak buffer utilization \- Buffer utilization of the switch (Polled every 50 µs).

Upon analyzing the data, the authors were able to show that correlated microbursts do exist in data centers and that congestion events observed by less granular measurements are likely collections of smaller microbursts.

They also find that link utilization is multimodal and that when these bursts occur, they are generally intense with Hadoop servers getting close to 100% utilization up to 10% of the time.

Another interesting network behavior observed is that at small timescales, the load can be very unbalanced and packets inside bursts tend to be larger than outside.

**Strengths, Weaknesses and Possible Follow-Up Ideas**

This is a well-written measurement paper that presents strong arguments for its case about making higher resolution measurements of network statistics.

However, the authors do not go into the details about the performance implications of deploying their high-resolution measurement framework on a large scale. Due to the compute and storage overhead of carrying out these high-resolution measurements, studying the real-world impact of a large-scale data center deployment will be helpful to understand the performance tradeoffs of these measurements.

The authors discovered that 60% of Web and Cache bursts start and finish within the 25 µs polling interval which may suggest that even this granularity is too coarse. A follow-up research idea is to increase the polling resolution, which may necessitate hardware support due to the latency between the CPU and ASIC used in the timing measurements.

**Lessons for Data Center Design**

The paper demonstrates the need for fine-grained measurements in order to truly understand network behavior in data centers. This can also affect the design and evaluation of new protocols and architectures. Since microbursts have been shown to be responsible for the majority of congestions in the measured data center, this has implications for load balancing, congestion control, and pacing.

Depending on the application behavior, lower-latency congestion control signals may be required since existing signals may not react to microbursts due to their incredibly short durations.
