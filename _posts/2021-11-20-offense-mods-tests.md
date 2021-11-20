---
layout: post
title:  "Offense Mods Tests"
date:   2021-11-20 09:11:34 +0000
category: roguelike
tags: code tests extension
---

# OffenseModsExtensionsTests
Not super exciting, but wrapping up the extension tests is important. The extensions on the OffenseMods are a little repettitive except for the types.  

## Tests
Just summing the values, but testing that all the object types actually work as expected.  

``` csharp
        [TestMethod]
        public void AddOffenseModsSumsValues()
        {
            var off1 = new OffenseMods(){totalAim = 18, totalDamage = 11};
            var off2 = new OffenseMods(){totalAim = 12, totalDamage = 9};
            off1.Add(off2);
            Assert.AreEqual(30, off1.totalAim);
            Assert.AreEqual(20, off1.totalDamage);
        }

        [TestMethod]
        public void AddItemSumsValues()
        {
            var off1 = new OffenseMods(){totalAim = 18, totalDamage = 11};
            var item = new Weapon();
            off1.Add(item);
            Assert.AreEqual(23, off1.totalAim);
            Assert.AreEqual(16, off1.totalDamage);
        }

        [TestMethod]
        public void AddOffenseSumsValues()
        {
            var off1 = new OffenseMods(){totalAim = 18, totalDamage = 11};
            var offense = new Offense();
            off1.Add(offense);
            Assert.AreEqual(21, off1.totalAim);
            Assert.AreEqual(16, off1.totalDamage);
        }
```

Again, just nailimg down function, simple tests.  

## Next Up
I still need to work up tests for Inventory and SortedList extensions, and then I need to work out where I'm headed next. Armor seems like a direction that will help expand the combat functionality and force me into another system, I think. I'll have to start thinking out the work items.  