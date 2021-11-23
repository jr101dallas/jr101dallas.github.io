---
layout: post
title:  "Collision Work"
date:   2021-11-24 09:11:34 +0000
category: roguelike
tags: plan system collision
---

# Work Items
Breaking down the work I think I'm going to do.... I'm going to use the name Collision, partly with the idea in mind that it could be an Entity colliding with a wall, an arrow colliding with an Entity, or maybe a club colliding with an Entity.  

## Steps
I think this is approximately where I'm headed.  

1. Create Collision System, implementing ISystem
1. add the random value to OffenseMods to keep it separate from the other values in case I want to do luck related things later
1. implement a public ResolveCollision, probably taking an OffenseMods and an Entity for now, maybe overloads later
1. implement the seam in Target.TargetEntity where the logic jumps to Collision

## Thought Process
Basically, we're still in the simplified case of targeting an adjacent Entity which can immediately resolve the collision on the referenced entity. When things start adding in the position information Target can be used to launch an OffenseMods packet with position and velocity and collision detection can happen and resolve collisions without even knowing where the offensive maneuver came from. It'll mean that eventually I'll need to add an Entity Id in OffenseMods for things like tracking stats and achievements related to combat but there's no sense in borrowing that trouble at this early stage.  
