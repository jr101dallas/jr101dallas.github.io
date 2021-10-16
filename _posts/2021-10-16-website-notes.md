---
layout: post
title:  "Website Notes"
date:   2021-10-16 09:11:34 +0000
category: website
tags: plan tutorial breadcrumbs
---

# Consolidation
I'm also pulling together the notes on how hte website and ci/cd is set up. Sixteen posts and maybe, hopefully, finished with messing with the website for a while.  

## GitHub Pages
There are two types of [GitHub Pages][pages] sites that I'm working with. I have a Project [site][roguelike] which is set up from this [repo][repo] and a User site which publishes from a specially named [repository][user-repo] with a format of ```<username>.github.io``` which I have publishing and can be viewed at [assumeforsimplicity.com][afs]. The Project site appears at the subdirectory ```/roguelike-tutorial/``` of my main site and I've done a little work to make it look seamlessly part of my main site.  

A large part of my site is built from the Quickstart and Tutorials in the [Jekyll docs][jekyll]. But, I've found a number of places where I got confused or made mistakes and things didn't work, or I had certain requirements that weren't met by the basics there.  

#### Source
One place I got tripped up between GitHub Pages types and different repository setups is the Souce setting in the repository Settings > Pages section. Here's how to tell what you need.  
* You want your website source at the root of the repository.  
  * select ```/ (root)```
* You want "other" things going on in your repository, not just the website.  
  * create first, then select the subfolder where you're going to keep the jekyll ```_config.yml``` the ```CNAME``` file and other website root elements.  
With the second option, you can keep whatever crazy train stuff in your root directory that you want and the website source files won't be confused with that stuff. In both cases, select the Branch you want GitHub to run the automatic Build and Publish on. More on the branch setting under GitHub Actions below.  

#### Custom Domain
Another place I got tripped up is teh Custom Domain setting. When you set it the first time, GitHub helps you create a ```CNAME``` file. The thing is, the ```CNAME``` file __IS__ the setting. It's a nice wizard clicky bit, but the ```CNAME``` file is the real deal. That means that if you change it or delete it, or change the Source (see above) folder or branch to someplace where the ```CNAME``` file isn't in the root of that setting, then your Custom Domain setting is lost! This becomes more important below when I get into GitHub Actions.  

## GitHub Actions
Depending on what you're doing with GitHub Pages and what your activity cycle is with your repository, you might not need any GitHub Actions configured. The reason for this is that there's a vanilla and automatic GitHub Pages build and publish workflow that triggers whenever your selected branch has a new commit. This could be you force pushing to it, merging a PR, whatever. This vanilla build and publish is the limited Jekyll build with certain whtelisted ruby plugins. It's an awesome way for people to get their website up and running immediately.  

#### Workflow
Special cases that have requirements outside the whitelisted plugins or, in my case, a need to decouple my coding activity schedule from my website's build and publish schedule, will need a workflow in GitHub Actions. In the Jekyll docs, there is a [section][jekyll-action] with good information on a workflow that does a custom Jekyll build and publish. When I was working through it, there was a little confusion between ```main``` and ```master``` which is the old deprecated ```main```.  

#### Workflow Source vs. GitHub Pages Source
The basic idea is that there is a __source__ branch for the workflow, and a __destination__ branch for the workflow, and in the doc there they are using ```main``` for the __source__ of the workflow and ```gh-pages`` for the destination of the workflow. The reason I'm trying to be specific here is that this is not the same as the Source setting in your repository Settings > Pages and that should probably be set to the __destination__ of the workflow unless you're doing something more complicated than I'm doing.  

#### Workflow Breaking Custom Domain
Above in the Custom Domain section I mention that changing the Pages > Source setting can break your Custom Domain setting unless you move the ```CNAME``` file to match the root of the new setting. Watch for this when you are configuring a workflow! You can solve it a few ways. One way that I failed to have work is to create the ```CNAME``` in the workflow destination and set up the workflow not to clear the destination before commiting the new files. Another way that I did get to work easily is to configure the workflow to copy the ```CNAME``` file from the source to the destination. I already had the ```CNAME``` file commited in ```main``` anyway and adding the include to the Jekyll ```_config.yml``` worked out great.  

[pages]: https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages
[roguelike]: https://assumeforsimplicity.com/roguelike-tutorial/
[repo]: https://github.com/jr101dallas/roguelike-tutorial
[user-repo]: https://github.com/jr101dallas/jr101dallas.github.io
[afs]: https://assumeforsimplicity.com
[jekyll]: https://jekyllrb.com/docs/
[jekyll-action]: https://jekyllrb.com/docs/continuous-integration/github-actions/