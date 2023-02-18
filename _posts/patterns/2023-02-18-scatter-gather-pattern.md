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
In this blog post, I will explore the Scatter-Gather Pattern, a cloud scalability pattern designed to process large amounts of data by breaking it down into independent tasks. By enabling parallel execution of requests on distributed systems, the pattern significantly reduces processing times.  

## What is Scatter Gather Pattern
The Scatter-Gather pattern is structured like a tree, with the controller acting as the root. When the controller receives an incoming request, it divides it into multiple independent tasks and assigns them to available processor leaves, which is the "scatter" phase. Each processor leaf works independently and in parallel on their tasks, producing a portion of the response. Once they have completed their tasks, each processor leaf sends its partial response back to the root controller, which collects them all as part of the "gather" phase. The root controller then combines the partial responses and returns the final result to the client. The pattern can be visualized with a diagram, as shown below.


![Scatter-Gather Pattern](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-02-18-scatter-gather-pattern/scatter-gather-introduction.png)*Scatter-Gather pattern illustration*

Let's take a hypothetical example to understand the Scatter-Gather pattern in more detail. Suppose we have a controller that can process an incoming request on a single-core processor, taking 64 seconds to produce a result. In a multi-threaded application running on a 16-core processor, the same result can be produced in slightly over four seconds. This is because sixteen threads can work in parallel to compute the result, with just a few extra milliseconds for the overhead of splitting the request and collecting the result. However, a four-second response time for a web application is slow and can interrupt a user’s experience. The Scatter-Gather pattern can solve this problem by distributing the request across multiple processors in multiple machines. Moreover, since we can scale horizontally in the cloud based on demand, the root controller can dynamically distribute the load based on the response time. If a particular node has a high response time (e.g., due to noisy neighbours), the root controller can redistribute the load for a quicker response. Let's examine a use case in the following section.

## Use Case
Now that we have a good grasp of the pattern, let's explore a practical use case where this seemingly straightforward pattern can demonstrate remarkable effectiveness. Imagine a website of a rail company that sells tickets to its customers. Suppose a customer plans a weekend trip from London and wants to optimize expenses by finding out how far they can travel with the same budget. They submit a query to the website, asking for a list of all direct trains departing from London throughout the country.