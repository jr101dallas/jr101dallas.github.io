---
layout: post
title:  "Back to Planning"
date:   2021-11-02 09:11:34 +0000
category: roguelike
tags: code plan target system
---

# Turn the Crank
Back on my Work Item Loop for another iteration, I've added some items to my [breadcrumbs](https://assumeforsimplicity.com/roguelike-tutorial/breadcrumbs/). But, I think I've got a little different direction I need to go in first.  

## Visible Functionality
The problem is that there isn't any visible functionality yet. So, I'm going to basically copy the logic from our last test, where an Entity is remove from play due to being targeted by another Entity, into Watcher in the Program.cs. Then, I'm going to do some work so that ```dotnet run -p .\Watcher\Watcher.csproj``` does something more interesting than just displaying:  

```
Hello World! Welcome to Utopia: The Tutorial
```

So, adding some flavor text to my methods like this...  

``` csharp
        public Entity GetEntity(int lastEntityId)
        {
            var newEntityId = ++lastEntityId;
            Console.WriteLine($"Entity {newEntityId.ToString()} has entered play!");            
            return new Entity(){
                Id = newEntityId
            };
        }

        public void TargetEntity(Entity originEntity, Entity targetEntity)
        {
            var removeEntityId = targetEntity.Id;
            Console.WriteLine($"Entity {removeEntityId.ToString()} has been removed from play!");
            _unv.entities.Remove(removeEntityId);
        }
```

I can now get some slightly more interesting output.

```
Hello World! Welcome to Utopia: The Tutorial
Entity 1 has entered play!
Entity 2 has entered play!
Entity 2 has been removed from play!
```

It's not much, but it's a start and it's visible! Now for a ```dotnet test``` and a ```git push``` before I start to do some more coding.  