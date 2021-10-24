---
layout: post
title:  "Entity Factory (Code)"
date:   2021-10-26 09:11:34 +0000
category: roguelike
tags: code entity factory
---

# Tieing In
I've created the Universe. That's never going to get old. Some of the things I get to say doing a game project are just too much fun. Last experiment with a game I had so much fun when I hit the point of implementing Death. This time it's still fun as I reimplement Death, or implement Death again!  

Universe is there but it's not hooked up. I'll need to instantiate it on game startup so that the Entity Factory has a place to put all the instances that we'll eventually be [scanning](https://www.youtube.com/watch?v=dWBmaKk32fE) for. Well, hopfully primarily we'll just be doing index seeks.  

## Instantiating Universe
I'm smiling, I hope you are too! I haven't really spent any time in physics.cs where Program and Main are. So let's fix that up a bit first.  

``` csharp
        static void Main(string[] args)
        {
            var gameName = GetGameDisplayName();
            var displayText = $"Hello World! Welcome to {gameName}";
            Console.WriteLine(displayText);

            var unv = new Universe();
        }

        public const string GameTitle = "Utopia";

        public static string GetGameDisplayName()
        {
            return $"{GameTitle}: The Tutorial";
        }
```

I've expanded on the ```Hello World!``` and that required the ```const``` and ```static``` keywords. Now we should get a nice little tag line in the console when we run our program. I probably need to be a little careful here because I really am going to end up breaking the logic out into a library eventually with the console application just picking it up and using it. There will almost certainly be some renaming involved since when I started out I had much less idea of where I was going.  

I've also instantiated Universe.  

## Entity Factory
Now I have a place for Entity Factory to list all the instances it creates. Getting an instance alone, in our case, wasn't good enough because we need to know when the instance should no longer exist and eventually when we tie in display when it should no longer be rendered or referenced.  