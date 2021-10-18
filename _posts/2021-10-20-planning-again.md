---
layout: post
title:  "Planning Again"
date:   2021-10-20 09:11:34 +0000
category: business
tags: plan arrange algorithm method habit
---

# Methodical
As we step further into the code in the roguelike, pay special attention to the method, the algorithm, that I'm following in my work. You've see a couple iterations now on my Coding Loop and it's time for another iteration on my Work Item Loop.  

## Why Tight Structure
Because I'm expected to be interrupted at any time, and because I work in short bursts, I have a requirement that I'm never very far from Capturing Gains that I've made. I need to capture gains frequently in order not to Lose Progress. It's a lot like saving often when playing a particularly difficult computer game.  

## Work Item Loop
In a larger company or project, there might be a team of people writing Stories or Work Items for software developers to code. I'm the only one working this project right now, 

...speaking of interruptions...

but the way of thinking when I'm planning functionality or architecture is different than the way of thinking to put the code in place that implements that functionality. Separation of concerns is important. Plan first, give yourself something to work through, make the separation clear so that you can get your thinking clear. Then, switch gears when you're ready to code.  

So, the loop is essentially:
1. plan some amount of items for work considering the bigger picture and write those items down  
1. start coding and work through the list until either I want to do some more planning or I run out and have to do some more planning  
1. repeat  

This keeps my focus tight on the tasks at hand. If I find myself trying to wander into architectural concerns, or new functionality, or gold plating, while I'm coding I try really hard to actually switch gears back to planning. Getting out of the coding details and up into the big picture planning helps me keep all the major strategic concerns in mind because that's where I'm looking instead of being distracted with how I'm going to write the code.  

## Coding Loop
My Coding Loop runs on a pretty tight algorithm too. I pick up a work item, checking that it's plenty small. At work I almost always break stories I get into much smaller pieces than how they're written. Then I start slapping up some files and classes, often getting a test in on the first line or two of actual functionality. The reason I do this is that it helps me nail down my thinking about how the code has to actually work to get the functionality I'm supposed to deliver. And, it helps me nail down details like when and where I need to initialize variables, whether I'm painting myself into a corner by letting concerns mix together, and helps me work interfaces in for easy testing seams and easy refactoring. After all, sooner or later, I'm probably going to decide that certain functionality needs to go into another NuGet package or project or microservice and having those seams built in makes those moves way easier.  

The loop here is a little different at work, and the full loop collapses a little into a simpler case on this project:

1. break the story or work item into bite sized chunks  
1. start roughing in files, classes, interfaces, and test classes, mostly empty but nailing down details consciously so that if I change my mind halfway through I catch it in the breaking tests when I drift out of my lane  
1. save files, run tests, check failures, failures are probably from half formed thoughts to start, a detail I left out  
1. work to the point where my test fails the way I actually intended it to fail, then fix the test  
1. look hard to see if there's refactoring to be done, running the tests often because they're actually guarding my functionality now  
1. grab the next bite sized chunk and repeat the rest of the steps  

I find lots of bugs in code, mine and other peoples', that are a result of a good plan that is missing details. The reliance on tests, early, tiny, and frequent, helps me fill in the details that maybe I would have otherwise missed. One more null check, one more condition in the code that takes a left turn and skips the telemetry calls, or the catch, or the validation, whatever. Thinking through the tests while I'm coding rather than after I'm finished coding covers the details better.  