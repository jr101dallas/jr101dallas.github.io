---
layout: post
title:  "Collision Work"
date:   2021-11-27 09:11:34 +0000
category: roguelike
tags: code collision system
---

# Remaining Items
Work items remaining, after tests, side quests, and experiments, are the following.  

1. implement a public ResolveCollision, probably taking an OffenseMods and an Entity for now, maybe overloads later
1. implement the seam in Target.TargetEntity where the logic jumps to Collision

It's somewhat entertaining to me still that I have so many surprises, gaps in requirements, and gaps in design, but I console myself claiming that I spend very little time on writing the work items. It's true, because I'm still writing requirements and work items for myself, but I'll continue practicing at doing it quickly AND more completely. It certainly can't hurt, and any gains will help here as well as in my real job.  

## ResolveCollision
Kind of tackling both of the remaining items together, going to need to work in some tests in a minute. Collision.ResolveCollision ends up looking like the code below for the moment, and Target.TargetEntity is updated accordingly. This means I'm a whole lot happier with TargetEntity.  

``` csharp
        public void ResolveCollision(OffenseMods off, Entity def)
        {
            if (!def.HasDefense(out var defenseComponent)) return;
            
            if (off.rngValue + off.totalAim > 50)
            {
                defenseComponent.CurrentHealth -= off.totalDamage;
                if(defenseComponent.CurrentHealth <= 0)
                {
                    Console.WriteLine($"Entity {def.Id.ToString()} has been removed from play!");
                    _unv.entities.Remove(def.Id);
                }
            }
        }

        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            var num = _rand.Next(100);
            var offenseMods = new OffenseMods(num);
            offenseMods.Add(GetEntityOffenseModifiers(originEntity));
            
            _unv.inf.collision.ResolveCollision(offenseMods, targetEntity);
        }
```

## Collision Testing
All the tests are still passing folloing moving the logic to Collision. Good start. I think that the actual Collision tests may just be a Target test that gets moved, or possibly just stripped down to the smaller scope of code.  

``` csharp
        [TestMethod]
        public void TargetEntityNotRemovedFromPlay()
        {
            var unv = new Universe();
            var targetEntity = unv.GetEntity();
            var tEntityId = targetEntity.Id;

            unv.inf.collision.ResolveCollision(new OffenseMods(), targetEntity);

            Assert.IsTrue(unv.entities.TryGetValue(tEntityId, out _), "Targeted Entity unexpectedly removed from play.");
        }

        [TestMethod]
        public void TargetEntityCurrentHealthIs45()
        {
            var targetEntity = new Entity();
            var collision = new Collision(new Universe());

            collision.ResolveCollision(new OffenseMods(), targetEntity);

            var defense = (Defense)targetEntity.components[ComponentType.Defense];
            Assert.AreEqual(50, defense.CurrentHealth, "Entity has unexpected CurrentHealth");
        }
```

Stripped down tests is where I ended up. It's actually a little funny since simplifying the test code might actually cause more code execution overall, but I'll decide if I'm worried about that later. Maybe adding a simpler constructor to Collision that doesn't need.... Nope, I just looked at that. The code references ```_unv``` to remove an Entity from play which could end up with a null refernce in my tests if I'm not careful.  
