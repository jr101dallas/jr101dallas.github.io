---
layout: post
title:  "ECS Planning"
date:   2021-10-17 09:11:34 +0000
category: roguelike
tags: plan ecs entity component
---

# ECS Design Pattern
E is for Entity that's good enough for me. I haven't really worked in the design paradigm of [Entity, Component, System](ecs), before so there might be some squirreliness in what I end up writing compared to people who have worked in game projects in the past. But, the basic idea is our Entity is a bag, and we stuff Components into it and then Systems run on the Components.  

[ecs](https://github.com/SanderMertens/ecs-faq)

## Entity Stuffing
We already have an Entity with an ```int``` id on it. For the moment, that should be fine. When we start having the problem of more entity instances than that will handle, we'll probably be at a different stage of growth and we'll have other things to worry about. Other than letting things get entrenched so that it's difficult to change our implementations we can just run with whatever implementations we think of. Along that same line, [Dictionary](dict) should be fine for stuffing them into our Entities and keeping the Components straight for the Systems to run on until we need something better.  

[dict](https://softwareengineering.stackexchange.com/questions/264766/efficiency-of-c-dictionaries)

## Short Term Plan
Based on all of that, we want:
1. An Interface that will be universal to all our Components to get stuffed into our Dictionary and consumed by our Services.  
1. A Class of Component that  implements the interface so that we can have some tests on Component.  
1. A Dictionary on Entity that takes the Component Interface.  
