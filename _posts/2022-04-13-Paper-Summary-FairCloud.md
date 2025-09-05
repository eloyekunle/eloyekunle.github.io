---
layout: post
title: 'Notes on "FairCloud: Sharing the Network in Cloud Computing"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.mosharaf.com/wp-content/uploads/faircloud-sigcomm12.pdf>

**Problem**

In cloud environments, CPU and memory are two important shared resources provided to tenants, and the amount shared is proportional to how much a tenant pays. Cloud providers also provide guarantees on these resources (performance etc). The network is also an important shared resource, but cloud providers rarely offered guarantees on the network bandwidth, and it is not scaled to payment.

Networks are difficult to share because a VM’s network allocation depends on the other VMs running on the same machine, as well as other VMs that communicate over each link used by the VM. In contrast, CPUs and memory do not have this complication.

**Approach**

The authors of this paper present a study of the problem of network sharing in cloud environments. They present three key requirements that network sharing should meet:

- **min-guarantee**: Tenants should have guarantees on the minimum network bandwidth.  
- **high utilization**: When network demands are low, active applications should be able to scale up bandwidth consumption, thus improving network utilization and boosting the applications’ performance.  
- **payment proportionality**: Network resources should be divided among tenants in proportion to their payments.

Using several examples, the authors show that there is a fundamental tradeoff between min-guarantee and network proportionality, and another tradeoff between network proportionality and high utilization. In order to properly navigate these tradeoffs, they define five useful properties: work conservation, strategy-proofness, utilization incentives, communication-pattern independence, and symmetry.

Building upon these, the authors propose three allocation policies that have different tradeoffs:

- **Proportional Sharing at Link-level (PS-L)**: Achieves link proportionality and can satisfy the five network properties listed above except strategy-proofness.  
- **Proportional Sharing at Network-level (PS-N)**: Provides better proportionality at the network level, but it does not fully provide utilization incentives.  
- **Proportional Sharing on Proximate Links (PS-P)**: Provides minimum bandwidth guarantees in tree-based topologies.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The paper convincingly argues that there cannot be a one-size-fits-all approach to network sharing while trying to balance between the three main requirements \- min-guarantee, proportionality, and high utilization. It is well-written, with a detailed explanation of the problem which aids understanding of the paper.

Some examples used to explain the problem seem to assume tenants’ knowledge of the cluster network topology and try to prevent behavior intended to exploit this knowledge (strategy-proofness). For most public cloud deployments, tenants don’t have much control over where their VMs are deployed in the cloud and where they are in relation to other tenants’ VMs. For data centers operated by single organizations, strategy-proofness may not be a priority since all “tenants” (which may be different teams) are part of the same organization. 

Overall, I think customer demand will be important to determine whether or not the implementation and deployment effort of these allocation policies in a data center will be worth it. A follow-up idea would be a concrete, deployable system containing the different network properties this paper presents, with options to create new policies by selecting among them.
