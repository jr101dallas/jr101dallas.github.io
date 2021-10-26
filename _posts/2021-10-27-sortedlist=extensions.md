---
layout: post
title:  "SortedList Extensions"
date:   2021-10-27 09:11:34 +0000
category: roguelike
tags: code sortedlist extensions research
---

# Reversal
So I changed my mind a bit. I was just planning on using a Dictionary again in Universe for tracking Entity instances just like I'm using on the Dictionary in Entity to track Components in Entity. But, I found that because I was using Entity.Id for the Key, which is an int, and because I really wanted to be able to grab Last() on the Key of the Dictionary of Entity instances so that I knew the next Entity.Id to create in the factory, I went looking to see if I could find something I liked better.  

## Research and Decisions
The use cases are close-ish, but I expect to maybe have thousands of Entity instances and they're easily ordered. I only expect to maybe have dozens of Component instances on an Entity and I expect to have the string be a name that I can easily refer to (maybe eventually an Enum) so they're not really all the way close. The search for the Last() function got me into the right place eventually. I found this link on [StackOverflow](https://stackoverflow.com/questions/1613004/get-last-element-in-a-sorteddictionary) about SortedDictionary and eventually landed on a SortedList with the extension methods suggested. So, Universe actually looks like this now.  

``` csharp
    public class Universe
    {
        public SortedList<int, IEntity> entities = new SortedList<int, IEntity>();

        public int LastEntityId()
        {
            if(entities.Count == 0) return 0;

            return entities.Max().Key;
        }        

        public Entity GetEntity()
        {
            var ef = new EntityFactory();
            var entity = ef.GetEntity(LastEntityId());
            entities.Add(entity.Id, entity);
            return entity;
        }
    }
```

The extension methods are as described in the link with links back to the post in comments. I figure it's best to leave a link in there for documentation of somebody else's cleverness and work.  

## And Testing
I also started getting my tests filled in again. It's definitely becoming clear that I'm working through a draft of the real tutorial. Working out details and finding my way through the actual code while working my method and checking if it works. I'm much more in teh middle of things than I want to be but it's not clear to me at the moment how I can break it into smaller functional chunks.  

These tests helped me find a bug:

``` csharp
        [TestMethod]
        public void DefaultUniverseHasZeroEntities()
        {
            var unv = new Universe();
            Assert.AreEqual(0, unv.entities.Count);
        }

        [TestMethod]
        public void DefaultUniverseLastEntityIdIsZero()
        {
            var unv = new Universe();
            Assert.AreEqual(0, unv.LastEntityId());
        }

        [TestMethod]
        public void FirstEntityHasIdOne()
        {
            var unv = new Universe();

            var ent1 = unv.GetEntity();

            Assert.AreEqual(1, ent1.Id);
        } 

        [TestMethod]
        public void FirstEntityAddsToEntitiesCount()
        {
            var unv = new Universe();

            var ent1 = unv.GetEntity();

            Assert.AreEqual(1, unv.entities.Count);
        } 

        [TestMethod]
        public void SecondEntityGivesLastEntityIdTwo()
        {
            var unv = new Universe();

            var ent1 = unv.GetEntity();
            var ent2 = unv.GetEntity();

            Assert.AreEqual(2, unv.LastEntityId());
        } 

        [TestMethod]
        public void FirstEntityRetrivedAfterSecondEntityCreated()
        {
            var unv = new Universe();

            var ent1 = unv.GetEntity();
            var ent2 = unv.GetEntity();
            var entityRetrieved = unv.entities[ent1.Id];

            Assert.AreEqual(ent1, entityRetrieved);
        } 
```

The bug was here:

``` csharp
    public class EntityFactory
    {
        public Entity GetEntity(int lastEntityId)
        {
            return new Entity(){
                //Id = lastEntityId++
                Id = ++lastEntityId
            };
        }
    }
```

That really got me wondering how I'd never really caught the difference before and how many times I've just been lucky with code using the increment operator.  

Maybe it was this test that was the actual one that I caught my problem on. The fact that I don't remember which is part and parcel with the fact and reason that I'm annoyed that I haven't managed to break this down smaller. On the whole, I don't think it's causing me a big problem but there's enough going on that it could have been. I'm lucky I'm in a good working streak and not in the middle of the holidays. Can't complain about good luck, but can't rely on it either.  

``` csharp
    [TestClass]
    public class EntityFactoryTests
    {
        [TestMethod]
        public void GetEntityZeroGivesIdOne()
        {
            var ef = new EntityFactory();
            var entity = ef.GetEntity(0);
            Assert.AreEqual(1, entity.Id);
        }
    }
```