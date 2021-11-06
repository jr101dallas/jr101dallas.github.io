---
layout: post
title:  "Item Aim Bonus"
date:   2021-11-10 09:11:34 +0000
category: roguelike
tags: code item component target bonus aim
---

# Work the Item Steps
Coding time again. Steps repeated for convenience.   

1. create an interface for targeting bonuses, name tba
1. create a class that implements IItem and IWhatsit, probably just Weapon for now
1. create tests for both interfaces in WeaponTests
1. swap out the Item being added to Inventory by default with the new class
1. add a GetAimBonus or whatever to the new interface
1. implement the logic for GetAimBonus in Weapon
1. add test checking the default value of the bonus in Weapon
1. add logic in Target to loop through entity items, sum the bonuses of the IWhatsit interface, and add to the calculation

## IWhatsit
My Aim Component has BaseAim in it. I'm thinking that I'm naming the interface for bonuses to Aim IAimBonus. Well.... . Aim might eventually have damage amount and type in it and a corresponding name change, and it could be bonuses or penalties, but I would expect the same interface would also handle those things too. So, going with IAimMods.  

Creating the Weapon class, implementing empty interfaces to start. Then I need the int for the... I did plan my tests first on purpose. So here are the basic interface tests on Weapon first.  

``` csharp
    public interface IAimMods
    {  
    }

    public class Weapon : IItem, IAimMods
    {        
    }

        [TestMethod]
        public void DefaultWeaponIsIItem()
        {
            var weapon = new Weapon();
            Assert.IsTrue(weapon is IItem);
        }

        [TestMethod]
        public void DefaultWeaponIsIAimMods()
        {
            var weapon = new Weapon();
            Assert.IsTrue(weapon is IAimMods);
        }
```

Start simple, confirm the tests work, no typos or mistakes. Ok, tests are passing.  

## Aim Bonus
Back to the int for the bonus, should be private, and the method in the interface and the implementation. Add the Test for the GetAimModifiers. I'm trying to lean harder into remembering the tests even in the planning. The value is so high that I think it's well worth working to improve my habits.  

``` csharp
    public interface IAimMods
    {
        public int GetAimModifiers();
    }

    public class Weapon : IItem, IAimMods
    {
        private int BonusAim = 5;

        public int GetAimModifiers()
        {
            return BonusAim;
        }
    }

        [TestMethod]
        public void WeaponBonusAimIsFive()
        {
            var weapon = new Weapon();
            Assert.AreEqual(5, weapon.GetAimModifiers(), "Weapon BonusAim isn't the expected value.");
        }
```

## Whoops
I skipped swapping out the default IItem in Inventory, so here's that piece. And I'm probably fixing a test.  

No test failures. I added a test so I won't miss that in the future when I make changes to the hardcoding.  

``` csharp
        public Inventory()
        {
            items.Add(1,new Weapon());
        }

        [TestMethod]
        public void DefaultInventoryItemOneIsWeapon()
        {
            var inventory = new Inventory();
            var firstItem = inventory.items[1];
            Assert.AreEqual(typeof(Weapon), firstItem.GetType(), "First item wasn't Type Weapon.");
        }
```

## Calculating Aim Modifiers
Last piece here is to plumb in the references and calculation to actually apply the motifiers to the targeting.  

``` csharp
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (!originEntity.components.TryGetValue("Aim", out var aimIComponent)) return;

            int aimMods = 0;
            if (originEntity.components.TryGetValue("Inventory", out var inventoryIComponent))
            {
                aimMods = GetAimModifiers(inventoryIComponent);
            }

            var aimComponent = (Aim)aimIComponent;
            var aimModifier = aimComponent.BaseAim;
            var num = _rand.Next(100);
            if (num + aimModifier + aimMods > 50)
            {
                var removeEntityId = targetEntity.Id;
                Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");
                _unv.entities.Remove(removeEntityId);
            }
        }

        public int GetAimModifiers(IComponent inventoryIComponent)
        {
            if (typeof(Inventory) != inventoryIComponent.GetType()) return 0;

            int aimMods = 0;
            var inv = (Inventory)inventoryIComponent;

            foreach(IItem item in inv.items.Values)
            {
                if (item is IAimMods)
                {
                    var modItem = (IAimMods)item;
                    aimMods += modItem.GetAimModifiers();
                }
            }

            return aimMods;
        }

        [TestMethod]
        public void GetAimModifiersSumsAll()
        {         
            var unv = new Universe();
            var inv = new Inventory();
            //inv.items.Add(1, new Weapon()); items[1] default hardcoded to Weapon
            inv.items.Add(2, new Weapon());
            inv.items.Add(3, new Weapon());

            var aimModsTotal = unv.inf.target.GetAimModifiers(inv);

            Assert.AreEqual(15, aimModsTotal, "aimModsTotal not expected value.");
        }
```

And, yes, that was more code than I'd really meant to have in a single step. Also, there's definitely more than one thing going on in TargetEntity so there's some refactoring that's going to need to happen. The Summary system I was thinking of would probably help sort out some of the complication in there.  