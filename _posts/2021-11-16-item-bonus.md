---
layout: post
title:  "Item Damage Bonus"
date:   2021-11-16 09:11:34 +0000
category: roguelike
tags: code item bonus
---

# Time
Still working on adding my Defense functionality. The next part I actually add is going to be the damage bonuses on the Weapon class and the calculations to figure it into actual damage dealing. Then maybe we take a look at items that either reduce damage, which is probably the same as the bonuses on the weapons but negative, and possibly ablative effects that reduce damage but cause wear on the item. I should probably take a quick look back at my plan, and of course I'm going a little more slowly right now because I'm looking at long and messy logic blocks and refactoring.  

## Checking Plan
I skipped item damage bonuses in my plan. I don't know how. But, here's some code and tests for exactly that because I had it in mind the whole time even though I never actually wrote it.  

First adjust Weapon.  

``` csharp
    public class Weapon : IItem, IOffenseMods
    {
        private int BonusAim = 5;
        private int BonusDamage = 5;

        public (int aimBonus, int damageBonus) GetOffenseModifiers()
        {
            return (BonusAim, BonusDamage);
        }
    }
```

Then follow the change to tuple up the stack and include the damage bonus in the calculation.  

``` csharp
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            if (!HasDefense(targetEntity, out var defenseComponent)) return;
            if (!HasOffense(originEntity, out var offenseComponent)) return;

            (int aimBonus, int damageBonus) offenseMods = (0, 0);
            if (HasInventory(originEntity, out var inventoryComponent))
            {
                offenseMods = GetItemModifiers(inventoryComponent);
            }

            var aimModifier = offenseComponent.BaseAim;
            var num = _rand.Next(100);
            if (num + aimModifier + offenseMods.aimBonus > 50)
            {
                var removeEntityId = targetEntity.Id;
                Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");

                defenseComponent.CurrentHealth -= offenseComponent.BaseDamage + offenseMods.damageBonus;
                if(defenseComponent.CurrentHealth <= 0)
                    _unv.entities.Remove(removeEntityId);
            }
        }
```

I'm not fond of declaring the tuple everywhere. It's too long for one and going to get longer, but the ability to just name the items willy nilly and confuse things throughout the code smacks of magic strings. I'd thought for a bit that the tuples might be the way to go, and they might still be, inside my class of combat bonuses, but not just raw like they are now.  


## Testing
The changes above caused some expected broken and failing tests, and that's great. There were also a couple of needed new tests.  

``` csharp
        [TestMethod]
        public void WeaponBonusDamageIsFive()
        {
            var weapon = new Weapon();
            var mods = weapon.GetOffenseModifiers();
            Assert.AreEqual(5, mods.damageBonus, "Weapon BonusDamage isn't the expected value.");
        }

        [TestMethod]
        public void GetItemModifiersSumsAllDamage()
        {         
            var unv = new Universe();
            var inv = new Inventory();
            //inv.items.Add(1, new Weapon()); items[1] default hardcoded to Weapon
            inv.items.Add(2, new Weapon());
            inv.items.Add(3, new Weapon());

            var mods = unv.inf.target.GetItemModifiers(inv);

            Assert.AreEqual(15, mods.damageBonus, "aimModsTotal not expected value.");
        }
```

And that gets the Item damage bonuses in place.  