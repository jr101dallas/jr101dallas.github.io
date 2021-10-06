---
layout: post
title:  "Rollercoaster"
date:   2021-10-08 09:11:34 +0000
---

# Broken CNAME
Apparently my broken Custom Domain setting for my GitHub Pages is still a thing. I found a page that indicated that this can be caused by a force push. It is possible that is exactly what my GitHub Action is doing and maybe most of the people using it don't have custom domains.  

## Combined Learning
Maybe, with what I've finally learned with this whole adventure, I can find a different action that will work or maybe I go back to the original action that I found and see if I actually do need what it's got. I'll kick around some research a bit here and see what I come up with and let you know below.  

## Documentation
I guess I should have read more of the documentation. There was a reminder at the bottom that if I'm using a branch other than ```main```, and I'm now using ```gh-pages```, then I need to manually create and commit a CNAME file to it. That at least appears to have fixed the immediate problem, but I'm curious about when the workflow runs in the GitHub Actions. I found some switches that appear to keep the history on the branch, ```keep_history``` on the Jekyll action, and ```keep_files``` in the Jekyll ```_config.yml``` that maybe also need to be set. I think I'm setting them now and hope it's not overkill to keep the CNAME file from the config settings. I'll let you know how it works out....  