---
layout: post
title:  "Component"
date:   2021-10-18 09:11:34 +0000
category: roguelike
tags: ecs component code
---

# Component
Back on coding! Well, roguelike coding anyway, not to insult the infrastructure as code guys doing the workflow and pipeline ci/cd work OR the web developers out there. But, those weren't what I was really trying to do.  

Component! It's time to add a Component.  

## Basics
I planned out my work first, in the last post, so I'm doing now. I'm creating an empty Interface, an empty Class, and a test that for now looks a little silly because it's just going to check that the Class implements the Interface.  

``` csharp
    public interface IComponent
    {  
    }
```

``` csharp
    public class Component
    {        
    }
```

``` csharp
        [TestMethod]
        public void DefaultComponentIsIComponent()
        {
            var component = new Component();
            Assert.IsTrue(component is IComponent);
        }
```

I'm sure you see where the failure for the test is happening, so I'll hook up the implementation and make the test pass.  

``` csharp
    public class Component : IComponent
    {        
    }
```

## Commit
These are tiny incremental changes. But, as I've said before I'm working in tiny bite sized chunks and this is a good place to stop and commit and capture my progress. I'm using git so that looks like this if you don't already know.  

Check which files I've changed or added.  
``` powershell
git status
```

If I only see my expected changes, and there should only be three since I'm keeping things really small: Component.cs, IComponent.cs, and ComponentTests.cs. Yep, those are the three I see in the status so, add, commit, push.
``` powershell
git add .
git commit -m "add Components"
git push origin
```
