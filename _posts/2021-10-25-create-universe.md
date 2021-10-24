---
layout: post
title:  "Create Universe"
date:   2021-10-25 09:11:34 +0000
category: roguelike
tags: code universe
---

# Foundation
The class Universe is looking like it's going to be a foundational building block to my game. It's going to have a Dictionary of all the Entity objects in it for lookup and removal, and that's going to be pretty important, so I'll put it straight in the root of the project and run with it.  

## Universe Code
Starting really simple, and building from there, is just an empty class to start. Though, things are still not as small as I was thinking, but not terrible with some copy paste magic.  

!. comment out the test I created before that turned out to be too big
1. create Universe as a near copy of Entity
1. remove Id from Universe, this is everything, not part of the multiverse
1. start to fix up the Dictionary and realize Entity doesn't have an interface yet
1. create IEntity as a near copy of IComponent
1. make Entity implement IEntity
1. create a test for Entity that parallels the Component one and checks that a default instance of Entity is an IEntity (this is because there are going to be assumptions based on it and future me might forget)
1. go back to Universe and switch up the Dictionary to be ```int, IEntity``` so that the internal Id will also be the key in the Dictionary

Code Tsunami....

``` csharp
    public interface IEntity
    {  
    }

    public class Entity : IEntity
    {
        public int Id;
        public Dictionary<string, IComponent> components = new Dictionary<string, IComponent>();
    }

    [TestMethod]
    public void DefaultEntityIsIEntity()
    {
        var component = new Entity();
        Assert.IsTrue(component is IEntity);
    }

    public class Universe
    {
        public Dictionary<int, IEntity> entities = new Dictionary<int, IEntity>();
    }
```