---
layout: post
title:  "Fast Start"
date:   2021-09-28 11:09:53 +0000
category: roguelike
tags: code plan
---

# Coding
Actual motion is good so rather than continuing to plan, let's slap some paint on the walls.  

## Current State
At the moment, I've got a solution ```utopia.sln```, and two projects added to it, ```physics.csproj``` and ```physics-test.csproj```. I don't need to get my complicated plan running right now with the library facades, or even worry about the things my selected libraries do. I can start working on my model of what happens in my logic and skip the other pieces. [SadConsole][SadConsole] does display stuff, so I'll avoid what things look like for a moment. [GoRogue][GoRogue] does mapping and object tracking, so I'll avoid questions of proximity for a moment.  

## What's Left
When I cut out the location and proximity quesions of GoRogue, and visual representation and layering of SadConsole, what do I still need to do?  

1. combat interaction (when to attack can be filled in when proximity exists)  
1. inventory (for now I won't "show" wearing armor in the display)  
1. dialog (again, proximity later)  
1. shopping (proximity later)  
1. enemies and merchants to fight and bargain with  

There are probably others, but these give us something to start with.  

[GoRogue]: https://github.com/Chris3606/GoRogue
[SadConsole]: https://sadconsole.com/
[helpers]: https://github.com/thesadrogue/SadConsole.GoRogueHelpers