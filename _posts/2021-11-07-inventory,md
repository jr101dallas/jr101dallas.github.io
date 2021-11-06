---
layout: post
title:  "Inventory"
date:   2021-11-07 09:11:34 +0000
category: roguelike
tags: code plan inventory component
---

# Carrying Stuff
I've got a few more workitems planned. Next on the list is the Inventory component. When I wrote out the workitem statements, it looked like this.  

- a Component with a list of Item instances, initially added by default to all Entity instances with the same Item

But, of course, I want to break it down smaller than that so I know where I'm going and which direction during the short work time I have.  

## Details
The main thing for Inventory is Items. I think I'm going to want to have an interface, because I'm pretty sure that there are things I'm going to want to do commonly to all items, possibly like check them all for breakage or combustion or to calculate the total weight or other fun stuff like that. Also, I'm going to need some kind of collection on the Inventory component to put the Items in. My first thought again is Dictionary so maybe we quick run through that thought process here.  

Item count in the Inventory could easily run into the hundreds. There could eventually be some in-game limiting factors like weight or slots but for the moment it's technically unlimited. Accessing the Item instances... hmmm. I'm sure I'll eventually want a display that lists all the Item instances probably with descriptions. Doing things with the items from the list, and possibly displaying an int/key for the items and then using my code operations on the int/key could be pretty easy. Or, if I decided to go by inventory slots, I could use a string key and the string would be the slot that the Item is in. I don't see a reason yet not to use Dictionary but deciding on the key may be something that I have to just go with something for a while and see how it works out.  

The defaults piece, adding a hardcoded Item to the hardcoded Inventory component, seems pretty straightforward.  

## List
So, summarizing my tasks from my work item, I think I'm headed for the following steps.  

1. create IItem interface
1. create Item class
1. create Inventory class implementing IComponent
1. add Dictionary to Inventory, key as int to start and value as IItem
1. hardcode Item instance add to Inventory in Inventory constructor for now
1. create Inventory in EntityFactory and add to Entity instance for now

I'm thinking that I also need to start considering a System for managing inventory. Names like Scavenge, Scrounge, or Packrat, are coming to mind. I'll have to start defining what would even happen there. Picking up things from the environment, dropping to the environment, maybe theft, maybe summary calculations like total weight. It bears more thought.  