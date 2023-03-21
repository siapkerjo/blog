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

![Monolithic Service Migration](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-03-14-orchestration-pattern/ProbContext-OrchestrationPattern.png)*Monolithic application migration to cloud*


## What is Orchestration Pattern
We have designed an appropriate architecture where all services operate within their [bounded context](http://ddd.fed.wiki.org/view/bounded-context). However, we still need a component aware of the entire business workflow. The missing element is responsible for generating the final response by communicating with all of the services. Think of it like an orchestra with musicians playing their instruments. In an orchestra, a central conductor coordinates and aligns the members to produce a final performance.


The Orchestration Pattern also introduces a centralized controller or service known as the orchestrator, similar to a central conductor. The orchestrator does not perform business logic but manages complex business flows by calling independently deployed services, handling exceptions, retrying requests, maintaining state, and returning the final response.

![Orchestrator Pattern Components](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-03-14-orchestration-pattern/OrchestrationPatternDetails.png)*Orchestrator Pattern*

The figure above illustrates the pattern. It has three components: the orchestrator or central service, business services that need coordination, and the communication channel between them. It is an extension of the [Scatter Gather pattern](https://www.gaurgaurav.com/patterns/scatter-gather/) but involves a sequence of operations instead of executing a single task in parallel. Let's examine a use case to understand how the pattern works.



## Use Case
Many industries, such as e-commerce, finance, healthcare, telecommunications, and entertainment, widely use the orchestrator pattern with microservices. By now, we also have a good understanding of the pattern. In this section, I will talk about payment processing, which is relevant in many contexts, to detail the pattern in action.

Consider a payment gateway system that mediates between a merchant and a customer bank. The payment gateway aims to facilitate secure transactions by managing and coordinating multiple participating services. When the orchestrator service receives a payment request, it triggers a sequence of service calls in the following order:
1.	Firstly, it calls the payment authorization service to verify the customer's payment card, the amount going out, and bank details. The service also confirms the merchant's bank and its status.
2.	Next, the orchestrator invokes the Risk Management Service to retrieve the transaction history of the customer and merchant to detect and prevent fraud.
3.	After this, the orchestrator checks for Payment Card Industry (PCI) Compliance by calling the PCI Compliance Service. This service enforces the mandated security standards and requirements for cardholder data. Credit card companies need all online transactions to comply with these security standards.
4.	Finally, the orchestrator calls another microservice, the Transaction Service. This service converts the payment to the merchant's preferred currency if needed. The service then transfers funds to the merchant's account to settle the payment transaction.

![Orchestrator Pattern Components](https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/2023-03-14-orchestration-pattern/OrchestrationPatternUsecase.png)*Payment Gateway System Flow*

After completing all the essential steps, the Orchestrator Service responds with a transaction completion status. At this point, the calling service may send a confirmation email to the buyer. The complete flow is depicted in above diagram. It is important to note that this orchestration service is not just a simple API gateway that calls the APIs of different services. Instead, it is the only service with the complete context and manages all the steps necessary to finish the transaction. If we want to add another step, for example, the introduction of new compliance by the government, all we need to do is create a new service that ensures compliance and add this to the orchestration service. It's worth noting that the new addition may not affect the other services, and they may not even be aware of it.





