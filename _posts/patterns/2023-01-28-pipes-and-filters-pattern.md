---
title: Pipes And Filters Pattern
description: 
tags: ["cloud", "pipes and filters", "design", "scaling"]
category: ["architecture", "patterns"]
date: 2023-01-28
permalink: '/patterns/pipes-and-filters/'
counterlink: 'patterns-pipes-and-filters/'
image:
  path: https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-01-28-pipes-and-filters-pattern/pipesAndFiltersCover.png
  width: 800
  height: 500
---

## Introduction
Applications today collect an infinite amount of data. Many applications need to transform this data before applying any meaningful business logic. Tackling this complex data or a similar processor intensive task without a thought-through strategy can have a high-performance impact. This article introduces a scalability pattern – pipes and filters – that promotes reusability and is appropriate for such scenarios.

## Problem Context
Consider a scenario where incoming data triggers a sequence of processing steps, where each step brings data closer to the desired output state. The origin of data is referred to as a data source. Examples of data sources could be home IoT devices, a video feed from roadside cameras, or continuous inventory updates from warehouses. The processing steps during the transformation usually execute a specific operation and are referred to as filters. These processing steps are independent and do not have a side-effect, i.e., running a step does not depend on any other steps. Each filtering step reads the data, performs a transforming operation based on local data and produces an output. Once the data has gone through the filters, it reaches its final processed stage where it is consumed, referred to as Data Sink.
A straightforward implementation could be a complete service that takes the data input, performs all the steps sequentially and produce an output. The modules within the service perform the required step and pass on the data to the next module. Although the solution initially looks good as it hides all the complexity of data processing, the use of such a monolithic service will have listed problems:
- The solution limits code reuse
- Any change in a processing filter step will lead to a release of all the filters
- The slowest processing filter step can become a bottleneck impacting the overall throughput of the service
- As the solution scales, it will scale all the processing steps. Such scaling will lead to excessive resource utilisation in the cloud when not intended.

Thus, the above approach is inflexible, non-scalable and against [reusability](https://en.wikipedia.org/wiki/Reusability){:target="_blank"}. A good solution must address all the above concerns. Let us try to find a solution this in next section.

![Monolithic Service](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-01-28-pipes-and-filters-pattern/patterns-pipes-and-filters-monolithic-service.png)*Monolithic Service with task modules*

## Solution
We can improve the suboptimal solution from the previous section by splitting the monolithic service into a series of components or functions, each performing a single processing filter step. These functions are combined to form a pipeline where functions receive a standard input and produce an accepted output. Such a decomposition will introduce loose coupling into the solution. It would allow effortless removal, replacement, rearrangement, or plug-in of a new filter step in pipelines. Moreover, it will enable code reuse if we need a similar processing filter in any other pipeline, the existing filters can be shared.

![Pipes and Filters Solution](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-01-28-pipes-and-filters-pattern/patterns-pipes-and-filters-solution.png)*Pipes and Filters Solution*

The solution also addresses the issue of the slowest processing filter step being a bottleneck, as it provides an opportunity to scale individual functions. The slowest running function can run parallel instances to spread the load and improve throughput. The individual scaling of components will also save us from any extra costs.

![Pipes and Filters Solution](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-01-28-pipes-and-filters-pattern/patterns-pipes-and-filters-solution-scale.png)*Pipes and Filters Solution Scaling Individually*

Finally, the processing filters can take advantage of cloud infrastructure. For instance, we can request a specific virtual machine based on the behaviour of a filter - processor-intensive or memory-intensive.

## When to use this pattern
As we now have a better knowledge of the pattern, this section explores when to think about the pattern.
1. The pattern is valuable for breaking down complex processing workflows, where each step is independent.
2. The processing steps have distinctive hardware needs or scalability requirements, and, in some cases, even preferred programming language requirements.
3. The pattern can address flexibility concerns in workflows where requirements and business processes are changing constantly.
4. The pattern is efficient in removing bottlenecks in workflows by parallel and distributed processing of individual steps. 
