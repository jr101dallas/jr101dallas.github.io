---
layout: post
title:  "Defense Mods"
date:   2021-11-29 09:11:34 +0000
category: roguelike
tags: code collision model defense armor
---

# Work the Plan
I'm starting to feel a little like these posts are a little repettitive, but also, I guess that's kind of the point. I've set out a system, to keep myself on track and moving forward. So, no apologies, here's the work list.  

1. create interface IDefenseMods
1. create an Armor class that is IItem and IDefenseMods
1. tests for Armor
1. add a default Armor instance to Entity
1. add Armor case to Entity tests
1. create a model for DefenseMods
1. add DefenseMods tests
1. create the extensions for adding DefenseMods
1. add tests for the extensions
1. set up use of the calculations in Collision
1. verify and possibly add to current tests around the calculations and removal of the targeted Entity

## Getting Started
Except for a pernicious typo in a filename, missing a file extension, the start of the work was easy enough. I did have to reorder some steps though because the interface needs the model. Here's the interface, model, and item.  

``` csharp
    public interface IDefenseMods
    {  
        public DefenseMods GetDefenseModifiers();
    }

    public class DefenseMods
    {
        public int totalAblation;
        public int totalArmor;
    }

    public class Armor : IItem, IDefenseMods
    {
        private int BonusAblation = 8;
        private int BonusArmor = 8;

        public DefenseMods GetDefenseModifiers()
        {
            return new DefenseMods()
            {
                totalAblation = BonusAblation,
                totalArmor = BonusArmor
            };
        }
    }
```

There's really only one test on the model. The tests on Armor are highly parallel to the tests on Weapon.  

``` csharp
        [TestMethod]
        public void DefaultDefenseModsHasValuesZero()
        {
            var defMod = new DefenseMods();
            Assert.AreEqual(0, defMod.totalAblation);
            Assert.AreEqual(0, defMod.totalArmor);
        }

        [TestMethod]
        public void DefaultArmorIsIItem()
        {
            var armor = new Armor();
            Assert.IsTrue(armor is IItem);
        }

        [TestMethod]
        public void DefaultArmorIsIAimMods()
        {
            var armor = new Armor();
            Assert.IsTrue(armor is IDefenseMods);
        }

        [TestMethod]
        public void ArmorTotalAblationIsEight()
        {
            var armor = new Armor();
            var mods = armor.GetDefenseModifiers();
            Assert.AreEqual(8, mods.totalAblation, "Armor totalAblation isn't the expected value.");
        }

        [TestMethod]
        public void ArmorTotalArmorIsEight()
        {
            var armor = new Armor();
            var mods = armor.GetDefenseModifiers();
            Assert.AreEqual(8, mods.totalArmor, "Armor totalArmor isn't the expected value.");
        }
```

## Remaining Steps
That leave the following steps still to go.  

1. add a default Armor instance to Entity(Factory)
1. add Armor case to Entity tests
1. create the extensions for adding DefenseMods
1. add tests for the extensions
1. set up use of the calculations in Collision
1. verify and possibly add to current tests around the calculations and removal of the targeted Entity

I noticed as I worked that I'd forgotten that I'd purposely chosen to put the component addition to the entity in EntityFactory and updated that step. The rest I think are still valid, at least for the moment.  
