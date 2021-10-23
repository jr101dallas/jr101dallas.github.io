---
layout: post
title:  "Component Plan"
date:   2021-10-22 09:11:34 +0000
category: roguelike
tags: plan component
---

# Combat Component
I didn't think of anything where I have a conflict with a combat component. So far, that seems like a chunk of data that hangs together and would be everything that a system that cares about resolving hits and damage would need. I'll run with it and see where we end up.  

## Function to Code
Repeating the list from before:

* ablation of damage and wearing out of related items  
* missing attacks due to high armor or dodge or illusion  
* injury  
* fatigue  
* damage resistance by damage types  
* temporary bonuses  
* innate permanent bonuses  

I need to break out some pieces of code, classes, properties, methods. I'll need some of these at least:

* Properties
  * int for hit points, how much damage before the entity is removed from the play area
  * int for causing misses
  * int for targeting
  * int for bursts of activity vs. constant motion
  * int for bonuses and penalties for each
  * enum for damage types

Yikes, there's a lot there. Maybe I can group some stuff out into classes. Even if this is one System that works on these, multiple classes may be a good idea.  

* Classes
  * hit points, damage adjustments, calculations, and types
  * targeting
  * hit resolution
  * burst activity

Ok, so bringing in some methods on the classes might look like this.

* Targeting, figuring origin entity stats
  * TargetAtRange
  * TargetAdjacent
  * TargetArea
  * AimCarefully
* Defending, figuring target entity stats
  * CheckImmunities, maybe an attack type is ignored
  * CheckDodge, maybe really hard to hit or really easy
  * CheckAblation, maybe armor is damaged and reduces or negates damage totals
* Injury (and healing)
  * Injure(type, amount)
  * Heal(amount)
  * AdjustForType(type, amount)

## Stuff to Do
I've got lists of stuff, concepts. If I start with the simplest case out of all that then it probably looks something like Entity1 targets Entity2 and Entity2 gets removed from the play area. That seems like a decent bite sized chunk so I'll go for it.  
