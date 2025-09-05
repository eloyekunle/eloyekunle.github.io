---
layout: post
title: 'Notes on "Understanding Lifecycle Management Complexity of Datacenter Topologies" (FatClique)'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://www.usenix.org/system/files/nsdi19-zhang.pdf>

**Problem**

Lots of data center topology design research has focused on improving performance properties such as bandwidth, latency, etc. However, lifecycle management of data centers is also important, since it can influence capital and operational expenses, and also ease of initial deployment and future expansion. It is important to consider future expansion, since data centers do not remain static over their lifetimes, and are frequently expanded and upgraded.

**Approach**

The authors decided to consider the lifecycle management implications of topology design. First, they define a set of metrics to evaluate topology designs, with a separate set of metrics for Deployment and another for Expansion. Based on these defined metrics, they propose and analyze a new class of topologies, called *FatClique*.

The complexity of laying out data center network topology (deployment) can be reduced by careful packaging, placement, and bundling strategies. Packaging is the arrangement of switches into racks, and placement involves the physical placement of the racks on the data center floor. With efficient packaging and placement strategies, one can realize better efficiency by bundling multiple fibers together.

The three key metrics that capture deployment complexity are numbers of **switches**, **patch panels**, and **bundle types**. More switches equal more packaging complexity, and the patch panels represent the topological structure and reflect wiring complexity.

The two key metrics that capture expansion complexity are the n**umber of expansion steps** and the **average number of rewired links in a patch panel rack**. Patch panel rewiring is important because it’s done manually, so this is an indication of the time it takes to finish the expansion step. Each expansion step requires reducing the data center capacity by a certain amount, called the expansion SLO, so keeping the number of expansion steps and necessary rewiring low can help minimize disruption during data center expansion.

A comparison of FatClique, Clos, Jellyfish, and Xpander reveals that FatClique is the best at most scales by all the defined complexity metrics. FatClique can permit speedy expansion while degrading network capacity by a small percentage. However, Clos can take 5x longer to do this. FatClique also requires fewer switches, patch panels (only at large scales, Clos has fewer at small and medium scales), and a lower cabling cost.

**Strengths, Weaknesses & Possible Follow-Up Ideas**

The evaluation metrics were well designed and explained, and the authors make an effort to evaluate the different networks fairly. However, the authors do not present a detailed performance evaluation of FatClique, which makes it difficult to understand how it performs in comparison with the other designs. Therefore, a possible follow-up idea would be to explore the performance characteristics of the network designs.

**Lessons for Data Center Design**

When evaluating the performance of network topologies, data center designers need to also consider the lifecycle management complexities. It’s important to be aware of the necessary tradeoffs, since a network design that performs well may be extremely difficult to expand or scale to future demands. Efforts should be made to design for manageability, as the management complexity of networks increases in the future.
