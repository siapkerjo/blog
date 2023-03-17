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
Many organizations are migrating to the cloud to leverage on-demand infrastructure and scale their applications. However, such migrations are never straightforward. We need established patterns and control points to manage the complexity. I have discussed a few of the known patterns in my previous blog posts. In this article, I will cover another scalability pattern called the Orchestrator Pattern. This design allows the building of scalable, reliant, and fault-tolerant systems. The pattern can manage the flow and coordination among components of a distributed system but is primarily used in a microservices architecture. Let us examine at a problem statement to understand it better.


## Problem Context
Consider a legacy monolith retail e-commerce website. This complex monolith consists of multiple subdomains such as shopping basket, inventory, payments etc. When a client sends a request to this application in the cloud, the website performs a sequence of operations to fulfil the request. In this traditional architecture, each operation can be described as a method call. The biggest challenge for the application is scaling with the demand. So, the organisation decided to migrate this application to the cloud. However, the monolithic approach that the application uses is too restricted and would limit scaling even in the cloud. Adopting a lift and shift approach to perform migration would not reap the real benefits of the cloud. 


Thus, a better migration would be to refactor the entire application and break it down by subdomains. The new services must be deployed and managed individually. These distributed and potentially stateless services are responsible for their own sub-domains. But the immediate question is how to manage a complete workflow in this distributed architecture. Let us try to address this question in the next section and explore more about Orchestration Pattern.





