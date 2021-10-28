---
layout: post
title:  "More Refactoring"
date:   2021-10-31 09:11:34 +0000
category: roguelike
tags: code test refactoring
---

# System Bag
I'm going to try the class to hold my systems first. I'm trying to think of a better name than Systems. But, I think I know where I'm going with it, so I'll start with that name and see if I come up with a better name on the way.  

This is what we're trying to un-ugly:  

``` csharp
            var target = (Target)unv.systems[nameof(Target)];
            target.TargetEntity(unv, originEntity, targetEntity);
```

## Infrastructure
I think I've settled on Infrastructure as my class that includes all the System instance for the Universe. It looks like this so far.  

``` csharp
    public class Infrastructure
    {
        private Universe _unv;
        public readonly Target target;
        public Infrastructure(Universe unv)
        {
            _unv = unv;
            target = new Target(_unv);
        }
    }
```

Because I'm planning on putting references to the Universe in all my System instances, I've gone ahead and added to the ISystem interface and updated Target.  

``` csharp
    public interface ISystem
    {
        public Universe GetUniverse();        
    }

    public class Target : ISystem
    {
        private Universe _unv;        

        public Target(Universe unv)
        {
            _unv = unv;
        }
        
        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            _unv.entities.Remove(targetEntity.Id);
        }

        public Universe GetUniverse() => _unv;
    }
```

And, that means Universe changes these...  

``` csharp
        public Dictionary<string, ISystem> systems = new Dictionary<string, ISystem>();

        public Universe()
        {
            LoadSystems();
        }
        
        public void LoadSystems()
        {
            systems.Add(nameof(Target), new Target());
        }
```

for these...  

``` csharp
        public Infrastructure inf;

        public Universe()
        {
            inf = new Infrastructure(this);
        }
```

## Final Verdict
After those changes, my Act in my test finally looks like this:  

``` csharp
            unv.inf.target.TargetEntity(originEntity, targetEntity);
```

And I feel a whole lat better about that.  