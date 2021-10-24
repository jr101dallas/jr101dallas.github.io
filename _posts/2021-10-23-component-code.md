---
layout: post
title:  "Component Code"
date:   2021-10-23 09:11:34 +0000
category: roguelike
tags: code component
---

# Coding Again
I'm about to start coding simplest case combat. __Target an enemy, hit lands, target is removed from play.__ I've got some fun games that don't have anything more complex than that so I'm actually pretty excited about this plan.  

## A Little Reorg
Really quickly, since I'm about to start adding a bunch of Components, I'm going to break out a Components folder and namespace for them. I thought about moving IComponent in there as well, but, that means my reference in Entity would be to Components. That seems sloppy. I want a reference to the interfaces but not actually to Components. So, I've also split out a folder and namespace for Interfaces. That way, if I decide I need to break out Interfaces into a NuGet package later it should be really easy. So a quick ```dotnet test``` after rearranging, then the old git status, add, commit, routine.    

## Start With a Test
I'm starting with a test this time, with probably additional tests to fill in the blanks as I go. I want to start with test that just says what I have above and then start back filling the code to make it actually compile. So, how about this to start.

``` csharp
        [TestMethod]
        public void TargetEntityRemovedFromPlay()
        {
            var originEntity = new Entity();
            var targetEntity = new Entity();

            originEntity.Target(targetEntity);

            Assert.IsNull(targetEntity);
        }
```

## Targeting Red Squiggles
That's a line in the sand anyway. I'm not so sure about the Target method at the moment since it doesn't seem like Entity should know about that, but I'll start working on it and see how it plays out.  

Whoops. Quicksand. I'll need to work out how to step into this in the tutorial. I started with my test and hit that little recursive problem:

1. need to call Target
1. shouldn't be on Entity, probably an extension method of the System
1. need Systems
1. Systems need to be able to tell the difference between Entity instances
1. need Entity tracking
1. Entity is all zero Id right now
1. need Entity Factory
1. ....

I'll cut this off here and spend some more time planning with respect to how to keep this small.  