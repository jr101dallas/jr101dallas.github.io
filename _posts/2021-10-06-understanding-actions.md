---
layout: post
title:  "Understanding Actions"
date:   2021-10-06 09:11:34 +0000
category: website
tags: workflow pages actions jekyll
---

# Got It
I have indeed been thinking about all of this incorrectly. Like when I was having confusion about the ```/doc``` folder, I just didn't get what was going on.  

## Source Setting
The GitHub Pages settings literally are looking for the source folder to build and publish from. Duh, right? But, the part that I wasn't thinking about correctly, is this: That's the folder that the vanilla GitHub Pages build and publish runs from. Good so far, right, but not enlightening yet. The GitHub Actions workflow Jekyll, yes, is doing a build and publish, but it's doing preprocessing to handle anything custom you need beyond the vanilla default and checks in to the target branch in order to trigger the vanilla one. Putting it another way, you can't change, configure, hijack, the vanilla GitHub Pages build and publish on checkin. What you CAN do is set up a GitHub Action for Jekyll with a schedule trigger to preprocess your real source into the source branch and folder that the vanilla build and publish then picks up. There are TWO build and publish actions happening in my repository now. The one that I've had all the excitement trying to figure out, and the vanilla one that is still running.  

## Landing
Where I've landed is this. My ```main``` branch is where I work and check in changes. Trunk based development, all good there. My GitHub Action is configured to pull from ```main``` and to publish to ```target_branch: 'gh-pages'``` which I found when I looked at the [source][jekyll-action] for the action linked at the bottom of the Jekyll doc page. So now it's not doing strange things with ```master``` anymore. AND, I had to change the source setting in the GitHub Pages settings to point at ```gh-pages``` so that, when the GitHub Action runs, it builds and publishes by checking in changes to the ```gh-pages``` branch and that checkin is the trigger that sets off the vanilla GitHub Pages build and publish which then shows up properly and on schedule on [assumeforsimplicity.com][afs]. Totally simple, right....

[jekyll-action]: https://github.com/marketplace/actions/jekyll-actions
[afs]: https://assumeforsimplicity.com
