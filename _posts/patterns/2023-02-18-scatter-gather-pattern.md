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


![Scatter-Gather Pattern](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-introduction.png)*Scatter-Gather pattern illustration*

Let us take a hypothetical example to understand the Scatter-Gather pattern in more detail. Suppose we have a controller that can process an incoming request on a single-core processor, taking 64 seconds to produce a result. A 16-core processor running a multi-threaded application can produce the same output in a little over four seconds. The processor will spawn sixteen threads to work in parallel to compute the result, with a few extra microseconds for the overhead of splitting the request and collecting the result. However, a four-second response time for a web application is considered sluggish and it can interrupt a user experience. The Scatter-Gather pattern can solve this problem by distributing the request across multiple processors on multiple machines. Moreover, the cloud provides allow us to scale horizontally on demand, the root controller can dynamically distribute the load based on the response time. If a particular node has a high response time (e.g., due to noisy neighbours), the root controller can also redistribute the load for a quicker response. Let us examine a use case in the below section.

## Use Case
Now that we have a good grasp of the pattern, let's explore a practical use case where this seemingly straightforward pattern can demonstrate remarkable effectiveness. Imagine a website of a rail company that sells tickets to its customers. Suppose a customer plans a weekend trip from London and wants to optimize expenses by identifying how far they can travel with the same budget. They submit a query to the website, asking for a list of all direct trains departing from London throughout the country.

One approach to handling this task is to send individual queries to each rail operator in the UK, collect the results, and then present them to the user. However, this can be a slow process as each query must be issued one at a time. To improve the efficiency of this process, the UK can be divided into five regions: Eastern, Northwest and Central, Wales and Western, Scotland, and Southern. When a request arrives, the root controller divides it and assigns it to the five leaf nodes to process in parallel. Each leaf node concentrates on a specific region, which reduces the need to search the entire country. Each of these leaf nodes then returns a list of all the direct trains from London to that region. Finally, the root controller merges all the results to create a final list to be presented to the customer.

## Implementation Details

The previous section has demonstrated a practical use case for improving the scalability of a web application. However, it is essential to consider the following points before adopting this tactic:

1.	Non-responsive leaf nodes
Even though the applications are hosted in a cloud environment, there is always a possibility that machines may become unavailable due to network or infrastructure issues. Moreover, the leaf nodes may communicate with external APIs (as we saw in the example use case previously) that may not respond promptly. To mitigate these issues, the root controller must set an upper limit on how long it can wait for a response. If a leaf node does not respond within that time, the root controller can ignore it and aggregate the results it has already gathered to formulate a response for the user. This approach can minimize the impact on the user by sacrificing a small portion of the result.

2.	Decoupling the Root Controller from Processing Leaf Nodes
It's crucial to avoid tightly coupling the root controller with the leaf nodes to improve the architecture. A tight coupling between them would mean that the root controller continuously tracks working leaf nodes, leading to unnecessary overhead on the application. Instead, we can use a message broker to design it. A pub-sub communication style can create a loose coupling between the root controller and the leaf nodes. When there is an incoming request, the root controller broadcasts messages to the potential working leaf nodes. All the working nodes can then subscribe to the incoming message, process it, and publish their results to a separate topic. The root controller can consume the results from that topic, aggregate them, and respond to the user.
