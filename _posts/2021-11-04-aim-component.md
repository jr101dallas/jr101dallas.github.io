---
layout: post
title:  "Aim Component"
date:   2021-11-04 09:11:34 +0000
category: roguelike
tags: code aim component
---

# First Component
The first of my planned steps involves my first real Component. Calling it Aim is what I'm thinking so far, but we'll find out whether that sticks.  

- a Component with a base targeting value, initially added by default to all Entity instances with the same value and used for determining success in Target

Breaking down my work a bit further gives me some more manageable steps:

1. create the Aim component
1. give Aim an int that counts for the base value, hardcoded value to start
1. add it to newly created Entity instances by default for now
1. add logic to Target to determine targeting success
1. add logic to Target to grab a source Entity Aim Component and take into account the base aiming int

## Ready, Go
Codewise, Aim is a copy paste from Component to start. I don't have a reason to inherit from Component but I want to implement IComponent for when I pass it into the Entity components dictionary. Adding BaseAim is the main differentiator, readonly for now. And of course tests.  

``` csharp
    public class Aim : IComponent
    {
        public readonly int BaseAim = 5;
    }

        [TestMethod]
        public void DefaultAimIsIComponent()
        {
            var aim = new Aim();
            Assert.IsTrue(aim is IComponent);
        }

        [TestMethod]
        public void DefaultAimBaseAimIsFive()
        {
            var aim = new Aim();
            Assert.AreEqual(5, aim.BaseAim);
        }
```

Where to add Aim to new Entity instances is maybe a little debatable. I thought about plunking it right into Entity but I'm really thinking that's too much built in. EntityFactory is where I'll eventually be generating piles of different kinds of Entity so that's where the Component add needs to happen. It'll probably eventually be farmed out to some other class, but for now I'm just going to plunk my code right straight into EntityFactory. And of course there's a test.  

``` csharp
        public Entity GetEntity(int lastEntityId)
        {
            var newEntityId = ++lastEntityId;
            Console.WriteLine($"Entity {newEntityId.ToString()} has entered play!");
            var newEntity = new Entity(){Id = newEntityId};
            newEntity.components.Add("Aim", new Aim());
            return newEntity;
        }

        [TestMethod]
        public void GetEntityDefaultsAimComponent()
        {
            var ef = new EntityFactory();
            var entity = ef.GetEntity(0);
            if (entity.components.TryGetValue("Aim", out var defaultAim))
            {
                Assert.AreEqual(typeof(Physics.Components.Aim), defaultAim.GetType());
            }
            else
            {
                Assert.Fail();
            }
        }
```

## Halfway
The only work left now is all in Target. I'm going to work in a random number generator and just check if it's over 50 out of 100 I think. Nice and simple and it gets me mostly where I'm going. The major problem is going to be what to do with testing. I may have to cheat for now and wait till I have a component pulled in for good random number functionality. I think GoRogue has a testing "random number generator" that produces a constant, for instance.  

``` csharp
        private Random _rand = new Random();

        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (_rand.Next(100) > 50)
            {
                var removeEntityId = targetEntity.Id;
                Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");
                _unv.entities.Remove(removeEntityId);
            }
        }
```

And now my test randomly fails!  

``` csharp
        [TestMethod]
        public void TargetEntityRemovedFromPlay()
        {
            var unv = new Universe();
            var originEntity = unv.GetEntity();
            var targetEntity = unv.GetEntity();
            var tEntityId = targetEntity.Id;

            unv.inf.target.TargetEntity(originEntity, targetEntity);

            Assert.IsFalse(unv.entities.TryGetValue(tEntityId, out _), "Targeted Entity still exists.");
        }
```

Well, the expected result is good even if it means there's more work still to be done that I hadn't worked into my planning steps. So now the last thing is to work in using the BaseAim from the Component.  

``` csharp
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (!originEntity.components.TryGetValue("Aim", out var aimIComponent)) return;

            var aimComponent = (Aim)aimIComponent;
            var aimModifier = aimComponent.BaseAim;
            
            if (_rand.Next(100) + aimModifier > 50)
            {
                var removeEntityId = targetEntity.Id;
                Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");
                _unv.entities.Remove(removeEntityId);
            }
        }
```

That leaves me with my randomly failing test, and some magic strings, and some other refactoring that I need to do, but the main hunk of code for this work item is complete! Now for the ```dotnet test``` a few last times to make sure it's still actually possible for all my tests to pass, then my ```git status``` routine and push.  