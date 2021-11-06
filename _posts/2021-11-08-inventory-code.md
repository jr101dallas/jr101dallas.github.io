---
layout: post
title:  "Inventory Code"
date:   2021-11-08 09:11:34 +0000
category: roguelike
tags: code inventory component
---

# Coding Stuff
List of tasks is ready, time to write some code. Repeating the list here for convenience.   

1. create IItem interface
1. create Item class
1. create Inventory class implementing IComponent
1. add Dictionary to Inventory, key as int to start and value as IItem
1. hardcode Item instance add to Inventory in Inventory constructor for now
1. create Inventory in EntityFactory and add to Entity instance for now

And, away we go!  

## Interface and Class
And a test, of course. Still at the early stage of the game, and still creating empty interfaces. Yeah, it feels a little unncessary but it's part of the expected long term strategy and hardly takes any time so I'm going ahead. It's little enough work and close enough to the expected design that I don't count it as [gold plating](https://blog.codinghorror.com/gold-plating/).  

``` csharp
    public interface IItem
    {  
    }

    public class Item : IItem
    {        
    }

        [TestMethod]
        public void DefaultItemIsIItem()
        {
            var item = new Item();
            Assert.IsTrue(item is IItem);
        }
```

## Inventory and Dictionary
Stealing existing code speeds this along. Copy Aim, replace the BaseAim line with the Dictionary, then I can just slap in a constructor and an Add to get an Item into the Dictionary. For tests, I can grab the contents of the ComponentTests and rename to start, but inventory has a Dictionary like Entity, so I can also grab a copy of the components test from EntityTests. Initially, the items (formerly components) test fails because I've hardcoded the Add for an Item in Inventory, so I can update the expected count in the test to reflect the current reality.  

``` csharp
    public class Inventory : IComponent
    {
        public Dictionary<int, IItem> items = new Dictionary<int, IItem>();

        public Inventory()
        {
            items.Add(1,new Item());
        }
    }

        [TestMethod]
        public void DefaultInventoryIsIComponent()
        {
            var inventory = new Inventory();
            Assert.IsTrue(inventory is IComponent);
        }

        [TestMethod]
        public void DefaultInventoryHasOneItem()
        {
            var inventory = new Inventory();
            Assert.AreEqual(1, inventory.items.Count, "Expected a single hardcoded Item in Inventory constructor.");
        }
```

## Entity Inventory
More hardcoding here for now. Every Entity has Aim; Every Entity has Inventory. The test is a copy of the test that checks that an Aim component is added to Entity by default. Remember that these tests are to explicitly state the current code shape and detect when we change things. If we end up with failing tests that we expected to fail, that's good, the tests are doing their job. If we end up with failing tests later when we didn't expect it, then they're still doing their job because they're letting us know what dominoes we're knocking over while we work.  

``` csharp
        public Entity GetEntity(int lastEntityId)
        {
            var newEntityId = ++lastEntityId;
            Console.WriteLine($"Entity {newEntityId.ToString()} has entered play!");
            var newEntity = new Entity(){Id = newEntityId};
            newEntity.components.Add("Aim", new Aim());
            newEntity.components.Add("Inventory", new Inventory());
            return newEntity;
        }

        [TestMethod]
        public void GetEntityDefaultsInventoryComponent()
        {
            var ef = new EntityFactory();
            var entity = ef.GetEntity(0);
            if (entity.components.TryGetValue("Inventory", out var defaultInventory))
            {
                Assert.AreEqual(typeof(Physics.Components.Inventory), defaultInventory.GetType());
            }
            else
            {
                Assert.Fail();
            }
        }
```

And that brings us to the end of the work item. Time for the ```dotnet test``` and ```git status``` dance! All the files are saved. Tests pass. Added and modified files list looks right. Commit, push, and go, go, go!  