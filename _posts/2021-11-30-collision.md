---
layout: post
title:  "Collision"
date:   2021-11-30 09:11:34 +0000
category: roguelike
tags: code collision tests
---

# Following Through
Moving on to the other open work items.  

1. add a default Armor instance to Entity(Factory)
1. add Armor case to Entity tests
1. create the extensions for adding DefenseMods
1. add tests for the extensions
1. set up use of the calculations in Collision
1. verify and possibly add to current tests around the calculations and removal of the targeted Entity

## Entity Armor
Uh, well, when I wrote up the work item about adding the Armor to Entity(Factory), I must not have been thinking straight. Armor is an item. I'll have to add it to Inventory. And, I'm betting I break some tests since I'm pretty sure I'm using defaults in a bunch of places.  

``` csharp
        public Inventory()
        {
            items.Add(1,new Weapon());
            items.Add(2,new Armor());
        }
```

The first try at adding the Armor broke 18 tests, because I was overwriting item 1 which used to be a Weapon with Armor. I probably need to add some methods for adding and removing inventory so that I don't start doing that a lot. I might be able to use the same max and min as on the entities in Universe. Once I got the index right, only three tests are failing.  

My first failure is here. And, that work to add and remove items would fix it. Right now I'm going to fix it manually and come back to the additional work.  

``` csharp
        [TestMethod]
        public void GetItemModifiersSumsAllAim()
        {         
            var unv = new Universe();
            var inv = new Inventory();
            //inv.items.Add(1, new Weapon()); items[1] default hardcoded to Weapon
            inv.items.Add(2, new Weapon());
            inv.items.Add(3, new Weapon());

            var mods = unv.inf.target.GetItemOffenseModifiers(inv);

            Assert.AreEqual(15, mods.totalAim, "aimModsTotal not expected value.");
        }
```

And, another failure is the same as the first, but the third one is this one, which used to expect one hardcoded item.  

``` csharp
        [TestMethod]
        public void DefaultInventoryHasOneItem()
        {
            var inventory = new Inventory();
            Assert.AreEqual(2, inventory.items.Count, "Expected two hardcoded Items in Inventory constructor.");
        }
```

That probably means that I should also move my hardcoded items out of the default Inventory and either add a constructor that lets me add in items as I create an instance of Inventory or possibly start in on some Entity instance definitions like Warrior, an Entity that has specific Offense values, Defense values, and Inventory. Plenty of near term targets!  

## Entity Armor Test
The real puzzler here though is my work item for an Armor case in EntityTests. I'm not sure what I was thinking here. There aren't really any Item or Inventory tests in there, like I'm sure you're thinking. I'm not ready yet for the tests in CollisionTests because I have work still to do in the DefenseModsExtensions before I can write those tests the way I want. Skip it for now, come back to it later. I've got a net zero for the night anyway if I add my new work and cross off the top two items.  
