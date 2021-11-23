---
layout: post
title:  "Random Value"
date:   2021-11-26 09:11:34 +0000
category: roguelike
tags: code model offense
---

# Bolt On
Adding the random value in OffenseMods looks pretty straightforward. The behavior of sticky and not changing is probably best achieved by just making the value ```readonly``` and it looks like just creating an instance in Target and calling add on it instead of the other instances should work out fine.  

## rngValue
I was looking for a good name like luckMod or totalLuck or rngLove, but I think that I just want it named really literally and I think I'll eventually have other values for Luck so I dont want to confuse things with that. So that means that the changes in OffenseMods look like this:  

``` csharp
        public readonly int rngValue;
        public int totalAim;
        public int totalDamage;

        public OffenseMods(){}
        public OffenseMods(int roll)
        {
            rngValue = roll;
        }
```

## Target Changes
Technically, I don't have to make any changes to the Add logic in the OffenseModsExtensions because I just want to do nothing with rngValue in the first place. Conveniently, rngValue being readonly will enforce that later when I forget. It does mean that I need to use the constructor for the first OffenseMods in Target.  

``` csharp
            var num = _rand.Next(100);
            var offenseMods = new OffenseMods(num);
            offenseMods.Add(GetEntityOffenseModifiers(originEntity));
```

## Unit Tests
The tests all pass still. I should figure out what tests I already need.  

I don't have any tests on OffenseMods at all yet, so there's a good target. Also, since I'm relying on specifc behavior based on rngValue being readonly, I should have a test around that. It's no good if I come back later and break an assumption that I'm relying on because I forgot.  

``` csharp
        [TestMethod]
        public void DefaultOffenseModsHasValuesZero()
        {
            var offMod = new OffenseMods();
            Assert.AreEqual(0, offMod.rngValue);
            Assert.AreEqual(0, offMod.totalAim);
            Assert.AreEqual(0, offMod.totalDamage);
        }

        [TestMethod]
        public void OffenseModsConstructorSetsOnlyRng()
        {
            var offMod = new OffenseMods(86);
            Assert.AreEqual(86, offMod.rngValue);
            Assert.AreEqual(0, offMod.totalAim);
            Assert.AreEqual(0, offMod.totalDamage);
        }

        [TestMethod]
        public void CanNotSetRng()
        {
            var t = typeof(OffenseMods);
            var rngValueFieldInfo = t.GetField("rngValue", BindingFlags.Public | BindingFlags.Instance);

            Assert.IsTrue(rngValueFieldInfo.IsInitOnly);
        }
```

Also, there needs to be a test on OffenseModsExtensions to confirm the Add behavior around rngValue.  

``` csharp
        [TestMethod]
        public void AddOffenseModsDoesNotChangeRngValue()
        {
            var off1 = new OffenseMods(8);
            var off2 = new OffenseMods(11);
            off1.Add(off2);
            Assert.AreEqual(8, off1.rngValue);
        }
```

## Collision
There isn't really any logic in Collision yet, so I'm going to skip making tests for it for now. BUt, I think that means I'm ready to get back on track with my planned work items.  