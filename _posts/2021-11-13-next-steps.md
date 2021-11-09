---
layout: post
title:  "Next Steps"
date:   2021-11-13 09:11:34 +0000
category: roguelike
tags: plan component
---

# Similar Functionality
With an Aim, BaseAim, and BonusAim, in place, damage and health seem like a good direction to go next. Looking at breacrumbs, I think the next few work items come from this list.  

- attack damage
- health
- item ablative effects
- item wear
- damage types
- damage type resistance
- inventory effects
- theft

## Starting Somewhere
We followed the path with Aim of creating the component, adding BaseAim stat, and then adding BonusAim to items and calculating and applying it. Following the same path seems not terrible. So that means deciding what component Health goes into, or BaseHealth. Then implementing that int, and then working in damage bonuses into items. I probably also need to decide there somewhere whether the damage bonus belongs in the same interface with the aim bonus.  

## Logic Flow
Most likely, I need to be considering how I want logic and calculation to flow through the systems. The idea so far is that an attack could be created, firing wildly, with targeting that could just be a trajectory and velocity, but the damage amount and type would be set. A melee attack, not ranged, would probably not bother with a trajectory, but instead have an Entity instance for a target. The difference between the two means that an attack would be launched separately from resolving the hit and effective damage. There needs to be a package, a class, that represents all the stats about an attack, effective aim, raw damage, damage type, at least. Target may well create these class instances on some kind of queue or collection in Universe, because they'll eventually have locations. Then, some other system, Collisions maybe, will watch locations for both an attack and and an entity and resolve the hit and damage.  

