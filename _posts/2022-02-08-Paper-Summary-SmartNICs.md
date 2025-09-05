---
layout: post
title: 'Notes on "Azure Accelerated Networking: SmartNICs in the Public Cloud"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/conference/nsdi18/nsdi18-firestone.pdf>

**Problem**

The previous network infrastructure for Azure was built on host-based software-defined networking (SDN) technologies, which provided flexibility and ease of management. However, with the rise in networking speeds, even highly-efficient SDN processing capabilities start to burn more CPU cycles than would be optimal. Therefore, the Azure team decided to explore a new hardware-based approach in order to reduce the high CPU requirement.

One of the main reasons for desiring lower CPU load is because the more CPU is needed for host-related activities, the less CPU is available to be sold to customers as part of VM offerings. Therefore, having more physical CPUs without trading off flexibility and network performance and possibly even gaining more efficiency was an important goal of this research.

**Approach**

The researchers set out to create a future-proof, hardware-based solution that would provide high performance and retain serviceability since it will be running in a public cloud environment. A number of potential hardware options were explored initially, such as ASIC-based NICs (lack programmability and adaptability), SoC-based NICs (poor single-flow performance, higher latency, can’t scale well to higher networks), and FPGAs, which seemed to be the best option. Microsoft already had some internal knowledge around FPGA programmability since they had previously used FPGAs in some other projects within Microsoft.

Typically, many performance bottlenecks related to hypervisor packet processing can be resolved by using hardware that supports SR-IOV. However, this completely bypasses the hypervisor thus making it impossible to implement SDN policies. Azure had an existing SDN policy provider called Virtual Filtering Platform (VFP). Therefore, the main goal for AccelNet was to somehow obtain the performance benefits of SR-IOV, without compromising on full policy and filtering enforcement of VFP.

The mechanism that does this is called Generic Flow Tables (GFT). When GFT sees a new network flow, it passes this over to the VFP software which then processes all SDN rules for the first packet of the flow and then caches these policies for the specific flow in the GFT. All future packets in the flow are then automatically processed by the GFT hardware.

**Strengths and Weaknesses**

FPGAs are programmed entirely in hardware description languages, which increases the barrier to the general adoption of the presented solution outside of huge companies with technical expertise. One point of comparison is TPUs used for accelerating machine learning training and inference, which are able to automatically run existing TensorFlow applications without the need for additional work by users.

However, the paper is very well-written and easy to read and understand. The necessary design goals, different hardware options, and also questions regarding FPGA’s suitability were well-explained. The solution they came up with was unique and well-suited to solve their goals exactly, which will probably be different for another kind of organization.

**Possible Follow-Up Ideas**

As other huge data center providers will need to solve similar problems in the future (e.g. Azure’s performance as presented in the paper completely blows out GCPU and AWS), then a good follow-up idea would be to create a hardware solution that is more easily programmable in popular existing languages, with accompanying software libraries.
