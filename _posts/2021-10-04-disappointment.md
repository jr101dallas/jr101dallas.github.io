---
layout: post
title:  "Disappointment"
date:   2021-10-04 09:11:34 +0000
background: assets\GitHubActionsGreen.png
---

# Bummer
According to the logging, GitHub Actions is running my workflow according to schedule. However, I don't see the next post showing on assumeforsimplicity.com and I was really hoping I had the problem solved. I'm going to hold off pushing through tomorrow and see if maybe I just still have something I'm not thinking of correctly in the timestamps before I invalidate the testing again.  

## Although..
I've also taken the opportunity to stop and test whether I can properly add images to my posts. I probably don't want to go crazy with images, but it's nice to be able to illustrate a point with fewer words. So, if I've done this correctly, you should see an image of the GitHub Actions logging. I know, exciting, right?  

## Double Checking
Now a little save, ```bundle exec jekyll serve``` and we'll know for sure. You can hold your breath, I won't finish this post before I have it working... probably.  

## Crash and Recover
It wasn't quite as easy as I had hoped, possibly because of the theme I've chosen. It sounds like some themes may be a little more full featured than others. But, with some reseach and mangling together of [things](https://stackoverflow.com/questions/54157109/how-to-render-background-image-in-a-page-generated-by-jekyll/54166941), I've got a workable solution. The link got me what I needed to know about the proper html, and the right placement in my posts layout, and the switch to ```page.background``` possibly poorly named, was my addition to my front matter. That way, a post that needs no picture, or a different one, doesn't have to suffer sameness. I may have to do some research on small images....

``` html
<div class="hero">
  <img class="feature-img" src="{{ page.background | relative_url }}" />
</div>
```