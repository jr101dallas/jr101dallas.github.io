---
layout: post
title:  "Item Aim Bonus"
date:   2021-11-09 09:11:34 +0000
category: roguelike
tags: plan item component target bonus aim
---

# Closing the Gap
With the big picture in mind, something along the line of targeting with an unwieldy rock is harder than targeting with a finely balanced sword, this step looked like the right size for a single bite. As I started to look at breaking it into little steps, I'm wondering if I misjudged again. Here's the statement for the work item I set out.  

- an Item instance that has an interface that adds a bonus to the success chance calculation in Target, initially defaulting an instance in every created inventory

It needs to be reworded a little for clarity.  

- an IItem instance with an additional interface that exposes access to a bonus int for Aim/Target calculations, initially defaulting an instance in every created Inventory

The rewording make more clear the involved strategy of the interfaces. I may end up with "Socks of Pussiant Aim +8" eventually. Things that aren't just weapons may eventually make their way into the group of things that add bonuses. That means if I'm inheriting classes all the time I could end up in a blind alley and have to copy bonus code around. If I go with an interface, anything could implement the interface and get swept normally into the same code path.  

## Steps
With the rephrasing, I can start working out steps I'm going to take to get the whole thing going.  

1. create an interface for targeting bonuses, name tba
1. create a class that implements IItem and IWhatsit, probably just Weapon for now
1. create tests for both interfaces in WeaponTests
1. swap out the Item being added to Inventory by default with the new class
1. add a GetAimBonus or whatever to the new interface
1. implement the logic for GetAimBonus in Weapon
1. add test checking the default value of the bonus in Weapon
1. add logic in Target to loop through entity items, sum the bonuses of the IWhatsit interface, and add to the calculation

That last step is larger than it looks, I think. Not terrible but definitely an implementation that will require future work. 

## Future State
There's every liklihood that I'm not going to want to loop all items on every entity whenever targeting happens. Since the next step is going to be adding and dropping items from Inventory, that may be a good time to work in some summarization logic. Inventory managment is often a pause in the action anyway, so looping the items and summing the bonuses there may not be noticable to the user. Having a summarization of stuff in Inventory for quick reference in other systems is probably a good performance direction anyway. It may end up as a Summary system.  
