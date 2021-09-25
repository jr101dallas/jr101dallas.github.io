---
layout: post
title:  "Project Layout"
date:   2021-09-27 11:10:19 -0600
---

# Requirements to Organization
Looking at my requirements, keeping things simple and fast is the big trick. Being able to work on a small section of code at a time will help a lot. To me, this means a few things. One is that I need to keep things in a model that I understand well rather than trying to conform to someone else's thought process.  

## Library Facade Projects
The packages I'm going to be using, I'm almost certainly going to want to build a layer of logic around so that I'm calling code that makes sense to me. If I'm using a library that serves breakfast, I want to make calls like "fry eggs" or "set table" instead of things like "set table height to 5" and "set egg flip time to 97 seconds." That means I want my dependency on a seperate project that wraps the library. In this case, I need to take a look again at the SadConsole GoRogue helpers to see if I'm going to have one wrapping project on that, or if I'm going to have separate projects for the two libraries separately.  

## Test Projects
Tests will also help keep my total context load and switching small. With good and plentiful tests I don't have to think quite as much about all the dominoes I'm about to knock over. I can just swing for the fence and watch to see what happens in the tests. If everything turns red, maybe I need to reconsider, or maybe I just need to follow the thread where it leads and fix the code and fix the tests as I go. Either way, I know immediately much more about the details of my decision without having to build it all in my thought palace first.  
