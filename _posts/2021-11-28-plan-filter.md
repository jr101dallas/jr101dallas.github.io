---
layout: post
title:  "Plan Filter"
date:   2021-11-28 09:11:34 +0000
category: roguelike
tags: plan collision system defense armor
---

# Reviewing Thoughts
Back when I was thinking about [Armor](/roguelike/2021/11/22/armor.html) in the first place, I needed a System that differentiated between Offense bonuses and defense bonuses. I actually spent some time thinking about that and pushing out the Armor post until I thought I knew what I was going to do. Now I have Collision already put together and I think I'm about to start on the parallel logic to IOffenseMods on Items.  

Thinking about OffenseMods, I can get an instance with values off of an Offense instance and off of anything that has IOffenseMods. If I create a DefenseMods... hangonaminutethough... the reason I have OffenseMods is that I expect that I want an Entity to launch an OffenseMods, have it careen around the play area and possibly hit something. Do I need a DefenseMods? It might still be useful along the lines of aggregating bonuses for defense, but I can't think of something that would have defense besides an entity. Except, maybe it's just a convenient place for the logic that is parallel to the extensions on OffenseMods.  

## Work Items
Steps I think I'm going to need to take are these:

1. create interface IDefenseMods
1. create an Armor class that is IItem and IDefenseMods
1. tests for Armor
1. add a default Armor instance to Entity
1. add Armor case to Entity tests
1. create a model for DefenseMods
1. add DefenseMods tests
1. create the extensions for adding DefenseMods
!. add tests for the extensions
1. set up use of the calculations in Collision
1. verify and possibly add to current tests around the calculations and removal of the targeted Entity

## Plan with Tests
I wrote my work items above without the tests again, but got down here and realized that they were missing and interspersed those steps. Practice, practice, practice. I'm guessing Im still missing some things, but I know where I'm going.  