---
layout: post
title:  "Testing Random"
date:   2021-11-05 09:11:34 +0000
category: roguelike
tags: code research testing random
---

# Not Random
I found at least one [answer](https://softwareengineering.stackexchange.com/questions/356456/testing-a-function-that-uses-random-number-generator) that was pretty much what I was thinking about my randomly failing unit test. Build an interface that my random number generator implements, and then build a mock that returns whatever number I want and also implements the interface.  

## Learning
However, I also saw an answer that mentions using a specific seed value for my random number generator. I think I'd heard those words together before but I hadn't really thought about it in much detail. But, sure enough, the documentation around the .NET Random [constructor](https://docs.microsoft.com/en-us/dotnet/api/system.random.-ctor?view=net-5.0#System_Random__ctor_System_Int32_) that allows you to set the seed backs up the idea and the approach. I'll need to read a little more closely before I decide what to do with my code. Either one of the approaches would work for what I'm going to need out of my code at this stage of the game, and most likely I'm just going to learn a bit and flip a coin.  

## Fun
There are some features that might be entertaining to implement around letting the player pick the random seed for the game. I'm thinking I should also do some research on games that are already doing that. Surely I'm not the only one thinking about fun things to do with that.  