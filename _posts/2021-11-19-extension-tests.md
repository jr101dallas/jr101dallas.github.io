---
layout: post
title:  "Extension Tests"
date:   2021-11-19 09:11:34 +0000
category: roguelike
tags: code extension tests
---

# Refactor Fun
Now that I have extension methods, I should have some tests on them. I'm only planning on testing the basics for now. EntityExtensions basically all depend on the HasComponent, so I'll test that it provides positive and negative results and that's pretty decent coverage. I could add some more to check things like positive results when there are multiple components, or negative when there aren't any components, but the logic is simple enough there isn't much point.  

## EntityExtensionsTests
Here are the tests on EntityExtensions.  

``` csharp
        [TestMethod]
        public void HasComponentTrueWhenExists()
        {
            var entity = new Entity();
            entity.components.Add(ComponentType.Inventory, new Inventory());
            Assert.IsTrue(entity.HasComponent(ComponentType.Inventory, out _));
        }

        [TestMethod]
        public void HasComponentFalseWhenAbsent()
        {
            var entity = new Entity();
            entity.components.Add(ComponentType.Inventory, new Inventory());
            Assert.IsFalse(entity.HasComponent(ComponentType.Component, out _));
        }
```

Short and simple.  

## OffenseExtensionsTests
Likewise, here are the OffenseExtensionsTests.  

``` csharp
        [TestMethod]
        public void ToOffenseModsReturnsOffenseMods()
        {
            var off = new Offense();
            var offMods = off.ToOffenseMods();
            Assert.AreEqual(typeof(OffenseMods), offMods.GetType());
        }

        [TestMethod]
        public void ToOffenseModsKeepsValues()
        {
            var off = new Offense(9, 11);
            var offMods = off.ToOffenseMods();
            Assert.AreEqual(off.BaseAim, offMods.totalAim);
            Assert.AreEqual(off.BaseDamage, offMods.totalDamage);
        }
```

I'm a little on the fence about multiple asserts in a single test. If they're all really extremely simple, I figure they're not so bad. But, I've had some really nasty complex tests with multiple non-trivial asserts in them where the failure wasn't immediately obvious despite the line numbers and failure text. The worst is when the test names are what show in the pipeline logging but then the multiple asserts are actually testing wildly different logic.  

## OffenseModsExtensionsTests
I'll come back to these, but for the moment I've got to get some sleep.  
