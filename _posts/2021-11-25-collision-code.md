---
layout: post
title:  "Collision Code"
date:   2021-11-25 09:11:34 +0000
category: roguelike
tags: code system collision
---

# Digging In
Heading into my work items I've set out and here's the list for convenience.  

1. Create Collision System, implementing ISystem
1. add the random value to OffenseMods to keep it separate from the other values in case I want to do luck related things later
1. implement a public ResolveCollision, probably taking an OffenseMods and an Entity for now, maybe overloads later
1. implement the seam in Target.TargetEntity where the logic jumps to Collision

Right off, as I start looking at the code, I've missed a work item.  

1. add Collision system to Infrastructure

I'm going to need to look at that a few minutes to figure out if I need to do anything differently from target. So far, I think I don't need the random number generator (RNG) but I'm definitely going to need to reference Universe still since I'll be removing Entity instances from play.  

## Infrastructure
That said, the changes to Infrastructure aren't large.  

``` csharp
        private Universe _unv;
        public readonly Target target;
        public readonly Collision collision;
        public Infrastructure(Universe unv, Random rng)
        {
            _unv = unv;
            target = new Target(_unv, rng);
            collision = new Collision(_unv);
        }
```

And, since I've already figured that piece out, the initial implementation of Collision is easy.  

``` csharp
    public class Collision : ISystem
    {
        private Universe _unv;

        public Collision(Universe unv)
        {
            _unv = unv;
        }
        
        public Universe GetUniverse() => _unv;
    }
```

## OffenseMods
Thinking about the RNG number result on OffenseMods has opened a pile of questions. Should I let it get summed like the other existing values? I don't think so, and I think whatever it gets set as first should be sticky. Although, maybe I need to break that thought process down further.  

* this isn't a value of some kind of luck stat or modifier from an item like a lucky rabbit's foot
* it is a number result from the RNG
* it should get generated when the attack is initiated
* it shouldn't get re-generated, or modified
* right now the only thing setting the value is the Target system, and that might stay that way

That probably means that I need to check and see how I need to work my logic in the Add extensions and in Target to see if I set the value when I create the initial instance of OffenseMods and set the value to readonly whether the logic already works or if I need to make further changes to set and keep the RNG value.  
