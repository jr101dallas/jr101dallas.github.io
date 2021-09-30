---
layout: post
title:  "Workflow"
date:   2021-10-03 09:11:34 +0000
---

# Progress?
Well, at least I know my workflow in DitHub Actions is triggering now. I went to look at the logging and found that the build was failing with an error.  

"No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository."  

## Google Search
A quick search for the error shows that I should be able to just add a repository value to the ```_config.yml``` like this for my repo  ```repository: jr101dallas/jr101dallas.github.io``` and it should be all better. I guess I'll know in a couple minutes.  

## Tripped Over
While I was there, I also noticed that the tag for ```author``` had been mispelled "auhor" so maybe I also found the problem with the author line in my Posts layout. Happy day, right? Also, definitely a great example of lived in code. Working around in the code and actually looking when I do things helps to catch the stuff that I haven't gone looking for. A million more years and commits and it should be perfect.  