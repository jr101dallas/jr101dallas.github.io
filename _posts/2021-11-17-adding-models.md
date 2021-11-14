---
layout: post
title:  "Armor"
date:   2021-11-17 09:11:34 +0000
category: roguelike
tags: code models offense
---

# Replacing Tuple
I had a little break, and when I came back the thing on the top of my mind was how I was not happy with the tuple situation. I wasn't all the way ready to add a Models namespace yet but it seems like now I'm going to.  

## Models
Just going to be some bags of values in here, but sometimes that's what you need. I'm starting off with OffenseMods in this case because the IOffenseMods primary method is GetOffenseModifiers (maybe soon to be shortened) and that's what is going to get returned. So here's the start of that code plus the resulting change to Weapon.  

``` csharp
namespace Physics.Models
{
    public class OffenseMods
    {
        public int totalAim;
        public int totalDamage;
    }
}

    public interface IOffenseMods
    {
        public OffenseMods GetOffenseModifiers();
    }

        public OffenseMods GetOffenseModifiers()
        {
            return new OffenseMods()
            {
                totalAim = BonusAim,
                totalDamage = BonusDamage
            };
        }
```

That makes the following changes happen in Target, and now I'm reminded how much I need to refactor some more in Target to simplify in there.  

``` csharp
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (!HasDefense(targetEntity, out var defenseComponent)) return;
            if (!HasOffense(originEntity, out var offenseComponent)) return;

            OffenseMods offenseMods = new OffenseMods();
            if (HasInventory(originEntity, out var inventoryComponent))
            {
                offenseMods = GetItemModifiers(inventoryComponent);
            }

            var aimModifier = offenseComponent.BaseAim;
            var num = _rand.Next(100);
            if (num + aimModifier + offenseMods.totalAim > 50)
            {
                var removeEntityId = targetEntity.Id;
                Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");

                defenseComponent.CurrentHealth -= offenseComponent.BaseDamage + offenseMods.totalDamage;
                if(defenseComponent.CurrentHealth <= 0)
                    _unv.entities.Remove(removeEntityId);
            }
        }

        public Universe GetUniverse() => _unv;

        public OffenseMods GetItemModifiers(Inventory inventoryComponent)
        {
            OffenseMods offenseMods = new OffenseMods();

            foreach(IItem item in inventoryComponent.items.Values)
            {
                if (item is IOffenseMods)
                {
                    var modItem = (IOffenseMods)item;
                    var mods = modItem.GetOffenseModifiers();
                    offenseMods.totalAim += mods.totalAim;
                    offenseMods.totalDamage += mods.totalDamage;                    
                }
            }

            return offenseMods;
        }
```

## And Tests
The tests really only needed some name changes since I renamed the int values in the OffenseMods but not really any other changes there.  

