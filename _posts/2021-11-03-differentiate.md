---
layout: post
title:  "Differentiate"
date:   2021-11-03 09:11:34 +0000
category: roguelike
tags: code plan inventory component
---

# Story
Having Entity creation, targeting, and Entity removal, is a good start to a roguelike in my opinion. However, there are a lot of things in and around targeting that differentiate one targeting attempt from another. For instance, attacking with an unwieldy rock, versus arracking with a finely balanced sword, there is an ease of targeting with a quality weapon designed for the purpose that an improvised weapon grabbed out of necessity simply doesn't have.  

## Inventory
If we're talking about what in game terms are bonuses or penalties to targeting, based on what is being used to attack, then we're talking about involving inventory at least. We're also talking about some kind of value representing how well an Entity can attack, and modifiers to that value according to items in inventory. Breaking it down further, I'm thinking that it's going to look something like this:  

- actually adding Component instances to Entity instances
- a Component for base targeting ability, plus at least a total of bonuses and penalties
- a definition of some kind for an Item
- a Component for a list of Items
- some way for adding and removing Item instances from Inventory
- an interface on some kind of Item that identifies it as having targeting bonuses or penalties
- additional logic in Target that uses the targeting ability for determining success or failure

## Ordering
To make smaller chunks of work, I think I can break this into four tasks and I'm planning on tackling them in the following order.  

1. a Component with a base targeting value, initially added by default to all Entity instances with the same value and used for determining success in Target
1. a Component with a list of Item instances, initially added by default to all Entity instances with the same Item
1. an Item instance that has an interface that adds a bonus to the success chance calculation in Target, initially defaulting an instance in every created inventory
1. adding and removing Items from Inventory

Trying to keep these really small and streamlined will be a little tough. I'm already thinking things like, "and add all the announcements to show in Watcher" or "create ten different Item instances for different weapons." Quick and simple is the name of the game. Completing small chunks that I finish quickly and check in. Capturing progress.  
