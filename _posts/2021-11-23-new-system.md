---
layout: post
title:  "New System"
date:   2021-11-23 09:11:34 +0000
category: roguelike
tags: plan system defense
---

# Target Versus Collision
I think, conceptually, the model I'm still operating on is that there are:  

1. attacks launched
1. packets of attack stats moving based on position and velocity
1. collisions based on position
1. resolution of collision based on attack stats versus defense stats

Slowly heading in this direction, with the Target system to start, and adding the OffenseMods model to carry the atack instance, leads me to the next step of creating the collision resolution system. The only name I'm thinking of so far is Collision.  

## Position
I'm still dragging my feet on adding the display and position functionality. That means I'm not working on actual position of launched attacks and triggering collision resolution based on whether there is an Entity in the same position. However, with the same assumptions I'm already using, some Entity launches an attack and some adjacent Entity is the target, meaning that the created attack immediately triggers collision resolution, I can just chain together the logic.  

The stage I'm at is that I'm actually going to break out the collision resolution into the Collision system. Collision could eventually split further when the position related collision detection comes into play, but for the moment it will just handle the armor and damage calculations.  

## Work Plan
Essentially, I need to decide where in this block of code from Target the seam needs to go to split code off into Collision.  

``` csharp
            var num = _rand.Next(100);
            if (num + offenseMods.totalAim > 50)
            {
                defenseComponent.CurrentHealth -= offenseMods.totalDamage;
                if(defenseComponent.CurrentHealth <= 0)
                {
                    Console.WriteLine($"Entity {targetEntity.Id.ToString()} has been removed from play!");
                    _unv.entities.Remove(targetEntity.Id);
                }
            }
```

If I pull the randome number generator (RNG) into Collision there's some plumbing to move around. I'm not sure I'm going to need RNG in Collision at all. Technically, the ```> 50``` part is what's going to get adjusted in the calculations in Collision so the if statement probably belongs there. That lands me on comething like Collision.Resolve with an argument of an OffenseMods instance getting passed in with the RNG value added into it, maybe now that I'm thinking of it, as a separate value.  

That's really a concept, a design idea, not a list of work items, so I need to work that up next.  