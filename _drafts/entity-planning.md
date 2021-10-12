---
layout: post
title:  "Entity Planning"
date:   2021-10-12 09:11:34 +0000
---

# E in ECS
E is for Entity that's good enough for me. I haven't really worked in the design paradigm of [Entity, Component, System](ecs), before so there might be some squirreliness in what I end up writing compared to people who have worked in game projects in the past. But, the basic idea is our Entity is a bag, and we stuff components into it.  

[ecs](https://github.com/SanderMertens/ecs-faq)

## Entity Stuffing
We already have an Entity with an ```int``` id on it. For the moment, that should be fine. When we start having the problem of more entity instances than that will handle, we'll probably be at a different stage of growth and we'll have other things to worry about but we should at least not write things in a way that will make it impossible to change things later. Along that same line, [Dictionary](dict) should be fine for keeping the Components straight until we need something better.  

[dict](https://softwareengineering.stackexchange.com/questions/264766/efficiency-of-c-dictionaries)

## Enter Component
For a Dictionary of Components, we need to create Components first! To get started on that 