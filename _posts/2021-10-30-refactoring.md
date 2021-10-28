---
layout: post
title:  "Refactoring"
date:   2021-10-30 09:11:34 +0000
category: roguelike
tags: code test refactoring
---

# The Yuck
In trying to keep to small work chunks, I wrote some pretty awful code.  

``` csharp
            var target = (Target)unv.systems[nameof(Target)];
            target.TargetEntity(unv, originEntity, targetEntity);
```

My thought process was:
1. keep a Dictionary of ISystem on Universe
1. load them when Universe instance is created
1. do stuff with them with great ease

I suppose that maybe I'm just having to do some acrobatics in the test, and not using them like they'll get used in a Universe instance, but I'm also feeling like I'm setting myself up to have to call everything with ```unv.Something``` to get anything done. It's probably time for more planning.  

## Not a Dictionary, Again
First off, I'm thinking I don't need Dictionary, or even a Collection at all, for my System instances. Even if I end up with thirty, I'm probably not ever going to have duplicates. Component instances, I could have duplicates, maybe I have two Locomotion Component instances, one for walking and one for flight. And, I'm likely to add and remove components from an Entity as items are picked up, dropped, and equipped. So, that still feels like a Dictionary because I'll still want to access them by name or Enum value, flight for the Locomotion Component that fills that use, equippedWeapon for the Weapon Component that the Entity has in its hand and ready. If I build my own object, I can have the different Types rather than using ISystem as well. I'll eventually need ISystem still, because I'll still eventually move at least some systems into microservices and those will all run things on the interface, but inside Universe I'll want reference to be the actual Types so I can have easy access to the unique things about each System.  

## Dictionary
Why my go-to is Dictionary, I don't know, but it does make an easy start to the code to get something in place quickly. But, at least I'm ready to abandon it when it doesn't make sense.  

What do I need in this object I'm designing? This seems easy, sensibly named objects for each System, already the correct Type that can just have its own methods called.  

Or what do I need in my Target class? It feels pretty obvious to me that there needs to be a reference to the Universe that the System is operating in. That would cut out having to tell the System every time where the Entity being operated on, or list of them, can be found.  

Or, what do I need in my ISystem interface? Maybe this is where the reference to the Universe belongs.  