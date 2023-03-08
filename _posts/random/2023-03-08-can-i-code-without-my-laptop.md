---
title: Can I Code Without My Laptop
description: The article discusses the comprehensive approach to delivering high-quality code, from business problem to producing robust maintainable code, considering technical strategies, deployment, security, and post-production activities.
tags: ["programming", "productivity", "ITlife"]
category: ["programming", "experience"]
date: 2023-03-08
permalink: '2023/random/can-i-code-without-my-laptop/'
counterlink: '2023-random-can-i-code-without-my-laptop/'
image:
  path: https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/random/2023-03-08-can-i-code-without-my-laptop.png
  width: 800
  height: 500
---


## Learning Adaptability
A few weeks ago, my laptop crashed during a meeting. It was painful as I was about to start on an exciting new feature that my Product Owner (PO) had just proposed. I immediately rushed to the IT department for assistance, and they informed me that they needed to take a backup and completely rebuild my laptop. They estimated that rebuilding would take slightly over a half day to complete.

Feeling frustrated, I asked myself: "Can I code without my laptop?". In the past, I would have answered 'NO' without hesitation. But on second thought, I realized that I know my system well and am also familiar with the domain. After more introspection, I recognized that I was already doing it without consciously realizing it. So, I went to my PO and requested him to print the new requirements for me.

## From Requirements to Success Criteria
There are numerous factors that a software engineer must consider before writing even a single line of code. First and foremost is understanding the business problem and who the actors are. Once you have a clear understanding of the requirements, it enables you to identify any flaws in the requirements or if it contradicts any existing features. You can then break it down into manageable pieces and think about how to reuse those pieces or determine if something already exists. This process helps you to define the final success criteria.

## Strategies and Considerations
Once you understand the problem landscape, the next step is to start thinking about solutions and strategies. Consider whether the operation will be compute-intensive or data-intensive. Can you offload any work to the client to reduce the server load? Are there any successful known solutions, patterns, or techniques that can be utilized? Should the results be cached, and if so, where? Additionally, consider breaking down the feature and delegating work to other team members to work in parallel.

In addition to the technical considerations, it's crucial to consider the nature of the application you're developing. Is it a web application or an API? You must also consider who will consume the service - web clients, iOS, Android, B2B, and B2C clients. Additionally, you need to focus on contracts and communication channels, both of which are crucial components.

## Beyond Development
Have we discussed security yet? Are you planning to place this new feature behind already available authentication flows and firewalls? Have you considered data in transit and data at rest? Are there any alerting mechanisms in place in case anything goes wrong?

So far, everything looks perfect. You know the problem, the strategy to develop the solution, the consuming client, deployment, and security. Once the feature is ready, it will deploy to production. However, have you considered what happens when it's in production? Do you need any configuration changes? Are there any potential obstacles to scaling the application? How will you address any performance issues that arise in production? Is there a prebuilt dashboard for reporting the critical metrics of the new feature?

## Producing Robust and Maintainable Code
Ahh, we have listed almost everything, and now it's time to start writing code. But, before you begin, you should consider the testability and maintainability of your code. You first think about your test before the code. Test Driven Development (TDD) should guide your development process to build a robust feature. This technique can help you solve problems more effectively and efficiently.

Now that you have completed the necessary groundwork, it's time to translate your well-thought-out heuristic into code using your preferred programming language and claim the story points.

## Journey to Deliver High-Quality
Junior developers may not have developed the ability to think that comprehensively yet, while seasoned software engineers know what it takes to deliver a component. And as you progress to be a tech lead, you will perform similar tasks, but for many components, simultaneously. Although it may seem like a lot of hard work, once you start doing it, it will become more intuitive. The only tedious part of this entire process is actually typing the code and committing it, for which I need my laptop.


