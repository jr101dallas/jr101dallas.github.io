---
layout: post
title:  "Intermediate Refactor"
date:   2021-11-18 09:11:34 +0000
category: roguelike
tags: plan item defense
---

# Halfway
I fell like I'm not finished in any way, but maybe hit a decent point for a pause. I still don't like several of my methods, but I'm closer.  

## Extension Methods
The main thing that I've done is moved some logic into better locations. I've created extension methods on my new OffenseMods and on Entity.  

``` csharp
        public static void Add(this OffenseMods addend1, OffenseMods addend2)
        {
            addend1.totalAim += addend2.totalAim;
            addend1.totalDamage += addend2.totalDamage;   
        }

        public static void Add(this OffenseMods addend1, IItem addend2)
        {
            if (addend2 is IOffenseMods)
            {
                var modItem = (IOffenseMods)addend2;
                var mods = modItem.GetOffenseModifiers();
                addend1.Add(mods);                    
            }
        }

        public static void Add(this OffenseMods addend1, Offense addend2)
        {
            addend1.Add(addend2.ToOffenseMods());
        }

        public static bool HasDefense(this Entity target, out Defense defCom)
        {
            bool foundDefense = HasComponent(target, ComponentType.Defense, out var defICom);
            defCom = (Defense)defICom;
            return foundDefense;
        }

        public static bool HasInventory(this Entity origin, out Inventory offCom)
        {
            bool foundInventory = HasComponent(origin, ComponentType.Inventory, out var invICom);
            offCom = (Inventory)invICom;
            return foundInventory;
        }

        public static bool HasOffense(this Entity origin, out Offense offCom)
        {
            bool foundOffense = HasComponent(origin, ComponentType.Offense, out var offICom);
            offCom = (Offense)offICom;
            return foundOffense;
        }

        public static bool HasComponent(this Entity entity, ComponentType type, out IComponent component)
        {
            return entity.components.TryGetValue(type, out component);;
        }
```

That shortens up some of Target pretty well.  

``` csharp
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (!targetEntity.HasDefense(out var defenseComponent)) return;

            var offenseMods = GetEntityOffenseModifiers(originEntity);
            var num = _rand.Next(100);
            if (num + offenseMods.totalAim > 50)
            {
                defenseComponent.CurrentHealth -= offenseMods.totalDamage;
                if(defenseComponent.CurrentHealth <= 0)
                {
                    Console.WriteLine($"Entity {targetEntity.Id.ToString()} has been removed from play!");
                    _unv.entities.Remove(targetEntity.Id);
                }
            }
        }

        public OffenseMods GetEntityOffenseModifiers(Entity entity)
        {
            var offenseMods = GetOffenseModifiers(entity);
            offenseMods.Add(GetInventoryOffenseModifiers(entity));
            return offenseMods;
        }

        public OffenseMods GetOffenseModifiers(Entity entity)
        {
            var offenseMods = new OffenseMods();
            
            if (!entity.HasOffense(out var offenseComponent)) return offenseMods;

            offenseMods.Add(offenseComponent);

            return offenseMods;
        }

        public OffenseMods GetInventoryOffenseModifiers(Entity entity)
        {
            var offenseMods = new OffenseMods();

            if (entity.HasInventory(out var inventoryComponent))
            {
                foreach(IItem item in inventoryComponent.items.Values)
                {
                    offenseMods.Add(item);                    
                }
            }

            return offenseMods;
        }

        public OffenseMods GetItemOffenseModifiers(Inventory inv)
        {
            var offenseMods = new OffenseMods();
            foreach(IItem item in inv.items.Values)
            {
                offenseMods.Add(item);                    
            }

            return offenseMods;
        }
```

## Inventory and Item
I'm not where I want to be with GetInventoryOffenseModifiers and GetItem0OffenseModifiers yet but the tests are passing and I need to stop for a bit.  