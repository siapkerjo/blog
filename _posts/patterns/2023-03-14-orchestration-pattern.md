---
title: 'Orchestration Pattern: Managing Distributed Transactions'
description: description
tags: ["cloud", "distributed systems", "design", "scaling"]
category: ["architecture", "patterns"]
date: 2023-03-14
permalink: 'patterns/orchestration-pattern/'
counterlink: 'patterns-orchestration-pattern/'
image:
  path: https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-03-14-orchestration-pattern/cover-image-orchestration-pattern.jpg
  width: 800
  height: 500
---

## Introduction
As organizations migrate to the cloud, they desire to exploit the benefits of this on-demand infrastructure to scale their applications. But such migrations are usually complex and require established patterns and control points to manage. In previous blog posts, I've covered a few of the proven designs. In this article, I'll introduce the Orchestration Pattern (also known as the Orchestrator Pattern) to add to the list. This technique allows us to create scalable, reliable, and fault-tolerant systems. The approach can help us manage the flow and coordination among components of a distributed system, predominantly in a microservices architecture. Let's dive into a problem statement to see how this pattern works.


## Problem Context
Consider a legacy monolith retail e-commerce website. This complex monolith consists of multiple subdomains such as shopping basket, inventory, payments etc. When a client sends a request to this application in the cloud, the website performs a sequence of operations to fulfil the request. In this traditional architecture, each operation can be described as a method call. The biggest challenge for the application is scaling with the demand. So, the organisation decided to migrate this application to the cloud. However, the monolithic approach that the application uses is too restricted and would limit scaling even in the cloud. Adopting a lift and shift approach to perform migration would not reap the real benefits of the cloud. 


Thus, a better migration would be to refactor the entire application and break it down by subdomains. The new services must be deployed and managed individually. The new system comes with all the improvements of distributed architecture. These distributed and potentially stateless services are responsible for their own sub-domains. But the immediate question is how to manage a complete workflow in this distributed architecture. Let us try to address this question in the next section and explore more about Orchestration Pattern.


## What is Orchestration Pattern
We have designed an appropriate architecture where all services operate within their bounded context. However, we still need a component aware of the entire business workflow. The missing element is responsible for generating the final response by communicating with all of the services. Think of it like an orchestra with musicians playing their instruments. In an orchestra, a central conductor coordinates and aligns the members to produce a final performance.


The Orchestration Pattern also introduces a centralized controller or service known as the orchestrator, similar to a central conductor. The orchestrator does not perform business logic but manages complex business flows by calling independently deployed services, handling exceptions, retrying requests, maintaining state, and returning the final response.


The figure above illustrates the pattern. It has three components: the orchestrator or central service, business services that need coordination, and the communication channel between them. It is an extension of the Scatter Gather pattern but involves a sequence of operations instead of executing a single task in parallel. Let's examine a use case to understand how the pattern works.








