---
layout: post
title: 'Notes on "P4: Programming Protocol-Independent Packet Processors"'
category: Blog
tags: [papers]
---

**Link to Paper:** <https://courses.grainger.illinois.edu/ece598hpn/fa2020/papers/p4.pdf>

**Problem**

Software-Defined Networking (SDN) provides data center operators with programmatic control over their networks. SDN physically separates routers into a *control* plane and a *forwarding* plane, and a single control plane can control multiple forwarding planes. The control plane configures the forwarding plane, while the forwarding plane actually processes the data request.

There are multiple forwarding devices from different hardware and software vendors, so the OpenFlow standard has been developed to provide an open, vendor-agnostic interface for programming these forwarding devices. However, the OpenFlow standard has gotten more complicated over the years as a result of trying to expose more switch capabilities to the controller. Therefore, there are a lot more header fields and multiple stages in the specification.

**Approach**

Instead of repeatedly extending the OpenFlow specification and making it more complicated, the authors of this paper argue for a more general, extensible approach by providing future switches with flexible mechanisms for parsing packets and matching header fields. Exposing these capabilities to controller applications would be a simpler, elegant, and more future-proof approach than the current OpenFlow standard.

For this, the authors introduced a new programming language for Programming Protocol-independent Packet Processors (P4). The three goals of P4 are:

- Reconfigurability \- Making it possible for programmers to redefine packet parsing and processing after deployment.  
- Protocol independence \- Making no assumptions about packet formats, but instead making it possible for programmers to define the format to support by specifying a packet parser for recognizing and extracting fields from the packet headers, and then processing these fielders with a collection of match+action tables.  
- Target independence \- Helping programmers create a target-independent description of the packet processing functionality without knowing the specifics of the underlying hardware, and then compiling this into target-dependent programs.

Whereas OpenFlow assumes serial execution of match+action stages, P4 supports parallel or serial execution. The language allows programmers to express serial dependencies between header fields, which lets it determine which tables can be executed in parallel. To facilitate dependency analysis P4 has a two-step compilation process: at the first level is the P4 program itself, which is then compiled to Table Dependency Graphs (TDGs).

A P4 program consists of four key components:

- Headers \- This describes the sequence and structure of a series of fields, including their widths in bits and constraints on their values.  
- Parsers \- Specifies how to identify headers  
- Tables \- Defines the fields on which a table should match and the actions it may execute.  
- Actions \- Helps to construct actions from a set of simpler protocol-independent primitives.

The authors provide a simple example that demonstrates P4 in action and discuss how each of the four key components listed above makes it work.

Whereas OpenFlow assumes a fixed parser, P4 also supports programmable parsers. For devices with programmable parsers, the compiler translates the parser description into a parsing state machine, while for fixed parsers, the compiler only verifies that the parser description is consistent with the target’s parser.

**Strengths & Weaknesses and Possible Follow-Up Ideas**

The approach presented in this paper can help accelerate the development of SDN by separating target device capability from the required effort by programmers, and also future-proofing the current OpenFlow interface. As described by the authors, this was also intended as a proposed idea for a future OpenFlow standard, and integrating this into the OpenFlow protocol itself will be a good next step.
