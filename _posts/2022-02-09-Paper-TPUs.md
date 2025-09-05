---
layout: post
title: 'Notes on "In-Datacenter Performance Analysis of a Tensor Processing Unit"'
category: Technology
tags: [papers]
---

**Link to Paper:** <https://arxiv.org/pdf/1704.04760>

**Problem**

During internal analysis of future traffic projections, Google realized that their data center computing infrastructure would no longer be adequate for their speech recognition DNNs. The general trend in computer architecture in recent years has been exploring domain-specific hardware to gain improvements in cost-energy performance. As a result of this, Google decided to build a custom ASIC to accelerate inference, while still using GPUs for training.

**Approach**

Google’s user-facing applications have rigid response-time limits, so all services they depend on, including the inference services, have to adhere to very rigid time limits as well. For TPUs, latency would be more important than throughput.

The heart of the TPU is the *Matrix Multiply Unit* which contains 65,536 8-bit MACs. The 16-bit products of the MMU are collected in an *Accumulator*. Weights for the matrix unit are staged through an on-chip *Weight FIFO* that reads from a DRAM called *Weight Memory*. An *Activate* instruction performs the non-linear function of the artificial neuron, reading from the Accumulators and writing intermediate results to an on-chip *Unified Buffer*. Afterward, data is written from the Unified Buffer into the CPU host memory.

The authors evaluated the performance of the TPU over two examples each of three popular types of neural networks \- Multi-Layer Perceptrons (MLP), Convolutional Neural Networks (CNN), and Recurrent Neural Networks (RNN). Using the roofline performance model for analysis, the different NN applications are generally further below their ceilings for the Haswell CPU and K80 GPU than they are for the TPUs which indicates their relative unsuitability for inference applications.

For the cost-performance comparison between the TPU, GPU, and CPU, they used the measure of performance/watt. The TPU is 17X-34X and 14X-16X better total-performance/Watt than Haswell and K80 server respectively. For incremental-performance/Watt, the TPU is 25X-29X better than K80. They also evaluated a hypothetical TPU die called *TPU’*, which contains some further architectural upgrades if they had *had* more time, such as using GDDR5 Weight Memory, aggressive logic synthesis, and block design. Cost-performance analysis shows the *TPU’* performing even much better than the TPU when compared with Haswell and K80 GPU.

**Strengths, Weaknesses and Possible Follow-Up Ideas**

The paper was well-written and the evaluations were well-presented. The hardware plug-and-play model makes it very easily deployed in data centers. TPUs also do not require any modifications to existing TensorFlow code which can make adoption much easier.

However, the TPU was compared with an NVIDIA GPU that was not optimized for inference, and which was an older chip at the time the paper was written. The Tesla P40 GPU is inference-optimized but the authors did not evaluate this because it did not offer SECDED. Generally, it would have been better to see a fairer comparison with contemporary GPUs at the time the paper was written, and this is also a possible follow-up idea for future research.

Since this paper focused on the performance of TPUs for inference, it would also be great to see how newer generations of the TPUs (which are capable of training) compare with GPUs in the training phase.
