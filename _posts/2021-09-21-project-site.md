---
layout: post
title:  "Project Site"
date:   2021-09-21 23:09:43 -0600
---

# Relatively Painless
The Roguelike Tutorial site is up! Hopefully, it looks pretty seamless with assumeforsimplicity.com, not that either one is a work of art at this stage, but the theme, and the navbar, etc. should be up and working.

## GitHub Project Pages
The configuration for the repository in Settings > Pages was pretty painless. Once I got the repository configured, the Pages setion announced at the top that the site is published at http://assumeforsimplicity.com/roguelike-tutorial/ so I set up the build to pull the page source from the ```main``` branch and the ```/docs``` folder. This time, since I'd already learned a bit about this setting from making mistakes in setting up my GitHub Pages User page, I knew where I was headed. I had sort of hoped that the project page would magically inherit from the User page but it didn't and it wasn't really a big deal to copy over the infrastructure files to get things matched up.

## Linking Relativism
One thing that did have to change was link paths in my ```_data/navigation.yml``` because they're separate sites even though I'm making them look more like they're all the same site. That means that my navbar links in roguelike-tutorial point to root for the green highlight when you're at http://assumeforsimplicity.com/roguelike-tutorial/ and external links for everything else. Different websites.

## Weblog Thoughts
I'm trying to figure out what I'm going to do with tutorial parts vs. blog posts and traffic and SEO and whatnot. It's not like I know a lot about that but I'm thinking that the tutorial parts might be blog posts in the Project site, probably with permanent links, whereas just my day-to-day blather on what I'm working on will go in my User site Posts. Maybe I'll get started that way and enjoy my favorite digital medium when I decide I need to reorganize everything.

## Almost Started
Overall, I'm pretty excited to be about ready to actually start the coding that I really wanted to do after setting up all the infrastructure to deliver the result to the outside world. It's funny how even I'm thinking complaints at the infrastructure stuff when I'm just trying to deliver value to my customers! Got to have infrastructure though, how else will anybody see the cool stuff they don't even know they need yet?
