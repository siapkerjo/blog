---
title: Pipes And Filters Pattern
description: 
tags: ["cloud", "pipes and filter", "design", "scaling"]
category: ["architecture", "patterns"]
date: 2023-01-28
permalink: '/patterns/pipes-and-filters/'
counterlink: 'patterns-pipes-and-filters/'
image:
  path: https://raw.githubusercontent.com/Gaur4vGaur/traveller/master/images/patterns/pipes-and-filters/pipesAndFiltersCover.png
  width: 800
  height: 500
---

## Introduction
Applications today collect an infinite amount of data. Many applications need to transform this data before applying any meaningful business logic. Tackling this complex data or a similar processor intensive task without a thought-through scalable strategy can have a high-performance impact. This article introduces another scalability pattern – pipes and filters - appropriate for such scenarios and promote reusability.

