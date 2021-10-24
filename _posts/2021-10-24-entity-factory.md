---
layout: post
title:  "Entity Fsctory"
date:   2021-10-24 09:11:34 +0000
category: roguelike
tags: code entity factory
---

# Backing Up
My small unit of work suddenly turned into a large unit of work with lots of recursive tendrils into things. I can over simplify and just drop down to the following I think.  

* empty System with an empty ISystem interface (these are nearly copy paste from Component for the moment)
* Target System with an extension method on Entity
* set the defending Entity to null (remove from play area)

Except, remove from play area isn't a concept that exists.  

## Remove from Play
If I were working on the display end of things, I would be just trying to make the Entity not display on the screen anymore, which in a prior experiment only ended up being an invisible player, entertaining but not intended. Since I'm not working with the display yet, something along the line of setting the object to null would be reasonable but it would also be missing the point of an Entity with an Id. So, I really need to work on generation and tracking of Entity instances with unique Ids.  

## Details, Details
For the moment, a Dictionary of Entities should work fine, again, by the time it doesn't work I'll have other problems. That leaves the question of where to put it. Eventually, I may have things like an Entity that isn't in play that could possibly return to play, or a player that is logged out, so if I name the class that holds the Entity Dictionary something like Universe that encompases all possible states and plan to have smaller lists eventually then I should be set up pretty well for the future.  

That gets me to a programmer Big Head statement, __"I have to create the Universe."__ And then I can create the Entity Factory which hands back an Entity and adds it to the Universe.  