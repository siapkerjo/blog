---
title: Scatter Gather Pattern
description: 
tags: ["cloud", "scatter gather", "design", "scaling"]
category: ["architecture", "patterns"]
date: 2023-02-18
permalink: 'patterns/scatter-gather/'
counterlink: 'patterns-scatter-gather/'
image:
  path: https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-cover-image.png
  alt: Image Credit & Copyright - https://www.facebook.com/masterdarksastro & https://www.hansonastronomy.com/
---

## Introduction
In this blog post, I will explore the Scatter-Gather Pattern. It is a cloud scalability pattern designed to process large amounts of data. The idea is to break down the operation into independent tasks. These tasks are executed on distributed systems to achieve parallelism. The approach can significantly reduce processing times.    

## What is Scatter Gather Pattern
The structure of the Scatter-Gather pattern is like a tree, with the controller acting as the root. When the controller receives an incoming request, it divides it into multiple independent tasks and assigns them to available processor leaf nodes, which is the "scatter" phase. Each processor leaf works independently and in parallel on its computation, producing a portion of the response. Once they have completed their task, each processor leaf sends its partial response back to the root controller, which collects them all as part of the "gather" phase. The root controller then combines the partial responses and returns the final result to the client. The pattern can be visualized with a diagram, as shown below.


![Scatter-Gather Pattern](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-introduction.png){: w="650" h="400"}*Scatter-Gather pattern illustration*

Let us take a hypothetical example to understand the Scatter-Gather pattern in more detail. Suppose we have a controller that can process an incoming request on a single-core processor, taking 64 seconds to produce a result. A 16-core processor running a multi-threaded application can produce the same output in a little over four seconds. The processor will spawn sixteen threads to work in parallel to compute the result, with a few extra microseconds for the overhead of splitting the request and collecting the result. However, a four-second response time for a web application is considered sluggish and it can interrupt a user experience. The Scatter-Gather pattern can solve this problem by distributing the request across multiple processors on multiple machines. Moreover, the cloud provides allow us to scale horizontally on demand, the root controller can dynamically distribute the load based on the response time. If a particular node has a high response time (e.g., due to noisy neighbours), the root controller can also redistribute the load for a quicker response. Let us examine a use case in the below section.

## Use Case
Now that we have a good grasp of the pattern, let's explore a practical use case where this seemingly straightforward pattern can demonstrate remarkable effectiveness. Imagine a website of a rail company that sells tickets to its customers. Suppose a customer plans a weekend trip from London and wants to optimize expenses by identifying how far they can travel with the same budget. They submit a query to the website, asking for a list of all direct trains departing from London throughout the country.

One approach to handling this task is to send individual queries to each rail operator in the UK, collect the results, and then present them to the user. However, this can be a slow process as each query must be issued one at a time. To improve the efficiency of this process, the UK can be divided into five regions: Eastern, Northwest and Central, Wales and Western, Scotland, and Southern. When a request arrives, the root controller divides it and assigns it to the five leaf nodes to process in parallel. Each leaf node concentrates on a specific region, which reduces the need to search the entire country. Each of these leaf nodes then returns a list of all the direct trains from London to that region. Finally, the root controller merges all the results to create a final list to be presented to the customer.

## Implementation Details

The previous section has demonstrated a practical use case for improving the scalability of a web application. However, it is essential to consider the following points before adopting this tactic:

### 1. Handling non-responsive leaf nodes
Even though the applications are hosted in a cloud environment, there is always a possibility that machines may become unavailable due to network or infrastructure issues. Moreover, the leaf nodes may communicate with external APIs (as we saw in the example use case previously) that may not respond promptly. To mitigate these issues, the root controller must set an upper limit on how long it can wait for a response. If a leaf node does not respond within that time, the root controller can ignore it and aggregate the results it has already gathered to formulate a response for the user. This approach can minimize the impact on the user by sacrificing a small portion of the result.

### 2. Decoupling the root controller from processing leaf nodes
It's crucial to avoid tightly coupling the root controller with the leaf nodes to improve the architecture. A tight coupling between them would mean that the root controller continuously tracks working leaf nodes, leading to unnecessary overhead on the application. Instead, we can use a message broker to design it. A pub-sub communication style can create a loose coupling between the root controller and the leaf nodes. When there is an incoming request, the root controller broadcasts messages to the potential working leaf nodes. All the working nodes can then subscribe to the incoming message, process it, and publish their results to a separate queue. The root controller can consume the results from that queue, aggregate them, and respond to the user. The figure below illustrates the implementation.

![Scatter-Gather Pattern Implementation](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-impl-decouple.png){: w="650" h="400"}*Scatter-Gather pattern implementation for decouples root controller*

### 3. Asynchronous request processing
The pattern implementation discussed so far works excellently for immediate responses, ranging from a few milliseconds to a couple of seconds. However, this implementation would not work for computation-intensive tasks, which can consume minutes to hours. Some examples of these computational tasks include data-intensive applications such as scientific simulations, data analytics, or machine learning. 
In these scenarios, it becomes necessary to split the root controller into two components that is a dispatcher and a processor.
<ol type="a">
<li> When a request comes in, the dispatcher accepts the request and generates a unique identifier. The dispatcher replies to the user with an estimated time to finish the task and provides the previously generated unique identifier to track the request.</li>
<li> The request is then published to the leaf nodes.</li>
<li> The leaf nodes process the request and send their results to a queue subscribed by the processor.</li>
<li> The processor service combines the results received from individual processor leaves and persists them into a database.</li>
<li> Once the estimated time has elapsed, the user can provide the unique identifier to fetch the result. If the result is unavailable within the estimated time, the processor can either request the user to check later or provide a partial result. </li>
</ol> 
The figure shows step by step asynchronous implementation 

![Scatter-Gather Pattern Asynch Implementation](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-impl-asynch.png){: w="650" h="400"}*Scatter-Gather pattern asynchronous implementation*

## Important Considerations – Number of Leaf Nodes
After seeing the implementation in the previous section, the next crucial step is to choose the optimum number of leaf nodes. Although the pattern seems to suggest that adding more leaf processors and increasing parallelization can improve efficiency, it comes at the cost of managing overhead. 
1. When a request comes in, the root controller must divide it, send it to the distribution infrastructure, and so on. While the cost of splitting, reading, and writing from the network is negligible at first compared to the computation logic, this cost grows as we scale leaf nodes. If parallelization continues, the overhead may exceed the computation cost.
2. The next consideration is that the root controller waits for the result from all leaf nodes, which means the overall response time depends on the slowest node. It is commonly known as the "straggler" problem, and it can considerably delay the system's completion time due to a single slow node. To mitigate the straggler problem, established techniques such as speculative execution and dynamic partitioning can distribute the load evenly.
3. The most favourable number of leaf nodes also depends on the availability and accuracy of service. Suppose a service wants to achieve 97% availability without sacrificing the result, and the cloud vendor environment provides a leaf node at 99% availability. In that case, choosing scatter-gather seems to be the right choice. However, a careful calculation reveals that the service can have at most three processing leaves. Adding a fourth processing leaf will lead to an SLA breach, as the probability of node failure (0.99 × 0.99 × 0.99 × 0.99 == 0.96). It gets even worse if we add more leaves.

Therefore, choosing the number of leaf nodes is critical for designing a high-performing distributed system.

## Summary
The scatter-gather pattern is a distributed computing pattern. It breaks down a single large operation into smaller, independent subtasks that can be processed parallelly across multiple nodes. The subtasks are then aggregated to produce the final result. The pattern has several advantages such as improved performance, increased fault tolerance, and better scalability. However, the choice of the optimal number of processing leaf nodes is vital to achieving high performance. Overall, the scatter-gather pattern is a powerful tool for developing high-performing distributed applications.