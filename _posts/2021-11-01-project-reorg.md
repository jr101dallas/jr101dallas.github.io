---
layout: post
title:  "Project Reorg"
date:   2021-11-01 09:11:34 +0000
category: roguelike
tags: code project solution dotnet
---

# Class Library
In building the ```physics``` project originally, I opted for a console project. As I kept building, I came more and more to the realization that I'm working on a class library and not a console app at all. How to correct the problem? I solved it with some messing around with the dotnet cli and some copy/paste magic.  

## Building Sideways
I switched to a different folder and "started over" so that I could see clearly what the raw dotnet commands produced. I did the following:  

``` powershell
dotnet new sln -n Utopia
dotnet new classlib -n Physics
dotnet new console -n Watcher
dotnet new mstest -n PhysicsTests
dotnet sln add .\Physics\Physics.csproj
dotnet sln add .\PhysicsTests\PhysicsTests.csproj
dotnet sln add .\Watcher\Watcher.csproj
cd .\Watcher\
dotnet add reference ..\Physics\Physics.csproj
cd .. 
cd .\PhysicsTests\
dotnet add reference ..\Physics\Physics.csproj
```

Once I could see the files created, I replaced the ones in my real repository folder and started cleaning up.  

- the old physics.cs is actually the new Program.cs in Watcher with the original Hello World! in it
- physicstests.cs goes away since I'm not ready to create a test project for Watcher yet
- I went ahead and renamed things like utopia to Utopia and physics to Physics

Once I thought I was done, I tried out ```dotnet test``` again and also ```dotnet run -p .\Watcher\Watcher.csproj```. I can definitely say that I didn't have everything right on the first try, don't think it was totally simple. On the other hand, the errors I got along the way were mostly clear. Once I looked at the files they were pointing at and what the command was that I was trying to run, mostly it registered what I'd done wrong and needed to fix.  

## Good Design
Some luck helped along with a the strategy above of comparing to something that worked the way I wanted. But also, the style of ```.csproj``` files that come with the dotnet cli helped a bunch too. The fact that there aren't references to every included file made it simple to drop in the new sln and csproj files and have things still mostly work. I wouldn't have been able to do that with the old Visual Studio generated files. Things are getting easier all over.  
