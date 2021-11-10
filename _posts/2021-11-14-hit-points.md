---
layout: post
title:  "Hit Points"
date:   2021-11-14 09:11:34 +0000
category: roguelike
tags: code component health
---

# Damage
The first place we're headed is health, damage, and bonuses. It's the closest parallel for the Aim classes and code, so this will probably go fairly quickly at first. That might afford me a change to pay more attention to the overall shape of the code and the logic flow while I work and maybe I can get in some cleanup.  

## Health
It seems like an int for health, a total of hit points, punishment that the Entity can take, is an easy implementation and not totally ridiculous concept, so, we start by mimicing Aim. Then Aim needs some updating. I'll think about the name, but it's basically the place where I want attack origination stats to live so it gets the BaseDamage int. Actually, I think I've just decided on Offense and Defense. Offense gets all the stats related to initiating an attack and Defense gets everything related to taking a hit and maybe not being removed from play.  

``` csharp
    public class Defense : IComponent
    {
        public readonly int BaseHealth = 50;
    }

    public class Offense : IComponent
    {
        public readonly int BaseAim = 5;
        public readonly int BaseDamage = 5;
    }
```

## Unit Test Value
And there's a big part of the reason for unit tests. I renamed a class and then got rid of errors, but that's not enough to prevent bugs.  

``` csharp
Test Run Failed.
Total tests: 24
     Passed: 22
     Failed: 2
```

Magic strings, causing a problem. I have some other renaming still to do, though I'm leaving BaseAim the same. But it's definitely time to work in a Component Type Enum. I'm assigning values to the enum members because I expect the list to grow and I have a slight preference for the members in alpha order and not to change values of the members. It might technically not matter, but it's also only another 10-20 keystrokes...  

``` csharp
    public enum ComponentType
    {
        Unknown = 0,
        Component = 10,
        Defense = 20,
        Inventory = 30,
        Offense = 40
    }
```

I'll have to catch the rest of this up later, but at the momemnt at least some refactoring is complete and tests are passing.  