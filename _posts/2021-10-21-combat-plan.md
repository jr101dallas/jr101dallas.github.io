---
layout: post
title:  "Combat Plan"
date:   2021-10-21 09:11:34 +0000
category: roguelike
tags: plan component combat
---

# Crystal Ball
Thinking out toward a roguelike game and trying to put into Components and Systems some of the things I think I want to have happen in the game, I think to start I'm going to put Entity conflict into its own Component and System. For the moment, I think that means that all the stuff like hit points, armor, damage resistance, weapon, damage amount and type, all goes into a Component that's basically just Combat. I'm second guessing myself as I write this, but I think I'm going to go farward in this direction for a while until I start seeing how my Systems start shaping up.  

## Combat Component
Listing out some things that I think I'll end up wanting to have happen in the game will help me flesh out the work items I'm going to hang on the bare bones ideas I have so far.  

* ablation of damage and wearing out of related items  
* missing attacks due to high armor or dodge or illusion  
* injury  
* fatigue  
* damage resistance by damage types  
* temporary bonuses  
* innate permanent bonuses  

## Simmering
I'll let that list simmer for a bit and see if I think of anything else that I need to plan ahead on. In the meantime I probably also need to come up with a list of other things that I need to have in components because it will help me figure out what belongs in which component. For instance:  

* combat, as above
* inventory
* communication? maybe reactions?
* skills? maybe several separate skills?
* relationships, maybe that's part of reactions
* locomotion
* knowledge, discovered information
* conditions, maybe not, keeping these with the things they affect seems better except for reporting on a heads-up display
* heads-up display
* stats, tallies per entity could be fun

That stirs up some ideas!  