---
layout: post
title:  "Target System Code"
date:   2021-10-28 09:11:34 +0000
category: roguelike
tags: code system target
---

# Groundwork
Finally, I have a class that contains tracking for all the entities I'm going to be creating, Universe of course. And I have a factory to help me make sure my entities are created with unique Id values and eventually to give me the right kind when there are multiple kinds. I even have tests that validate that creating two entities in a row doesn't annihilate the first one created and that I can get back the first entity from my storage in Universe. I have an actual definition of an Entity being "in play."  

## Ta Da!
That means that I should actually be able to support a test that creates two distinct Entity instances that are durable and retrievable from Universe and have one target the other and the target can be verifiably removed from the play area. I do also think that I'm realizing that part of the problem with my "not small enough chunk of work" is that I didn't really complete the thought of Entity as being a bucket of Components with an Id. When I run back through this in the tutorial, there should be more parts to implementing Entity before I start trying to add Component. Live and learn.  

## Goal
The test that I created and then had to comment out because I was just too far away in terms of work is this one, now uncommented again.  

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

You can't see it here, but I still have a red squiggly for ```.Target(targetEntity)``` because it still doesn't exist. It's still in my EntityTests.cs which is probably the wrong place for it, but now I think I'm actually not far from having the right place.  

## Coding
So I'm technically hitting a ```git stash pop``` to pull back out some files I started putting together before I realized I was too far away and not ready. Here are the System interface and Target class to start.  

``` csharp
    public interface ISystem
    {  
    }

    public class Target : ISystem
    {        
    }
```

Again, trivial to start. And it affords me a place to land that test, ```TargetTests.cs```, that actually makes sense. But, now I need to work in that "removed from play" concept. Universe isn't in my test and "in Universe" is "in play." That means that the first part of my test needs to look more like this:  

``` csharp
            var unv = new Universe();
            var originEntity = unv.GetEntity();
            var targetEntity = unv.GetEntity();
```

But now I need to think some more about how the Target system, and probably all my systems, need to work. And that probably means that I've stumbled over the second half of the "too big a work chunk" problem.  