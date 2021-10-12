---
layout: post
title:  "GitHub Pages Types"
date:   2021-09-14 09:35:11 +0000
category: website
tags: github
---

# Broken Site
Moving fast and trying to get this site set up, I tripped over a detail. I'd seen that there were two types of github Pages sites, and set a configuration that I thought I wanted. I felt like I'd rather use the ```/docs/``` subfolder than the ```gh-pages``` branch for the website source. It seemed like the right way to go.  

And then, late at night, I triumphantly push my source for my totally awesome and totally __working locally__ site!!!  

And it doesn't work on [assumeforsimplicity.com](https://assumeforsimplicity.com)....

I left myself some breadcrumbs because it was way past bedtime, and left the site broken. It hadn't been functional before, but it was empty. Now it was broken.  
* TODO: Fiddle with layout for posts not showing author  
* TODO: Troubleshoot local render difference from github render, missing navbar  
* TODO: figure out why site is borked  

## Documentation For Pages Source
Starting at the link [Types of GitHub Pages](page-types) and heading on down through the part about the ```/docs``` folder and the ```CNAME``` file is what you might need to know if your site is working correctly locally and not working on ```<username>.github.io``` and you don't know why.  

My site is a bit muddled now with the confusion about where the source is coming from, but I think I at least know where I'm headed now.  

## Details for The Interested
I'd set my GitHub repository Pages settings to ```main``` and ```/docs``` somehow conflating things. I remember part of the thought process nine months ago was that the ```/docs``` subfolder for the source in the ```main``` branch allowed me to continue following my normal git process, trunk based, rather than trying to juggle branches. Somehow, the execution of that with the tutorials on [Jekyll docs][jekyll-docs] got confused and what was actually published on [assumeforsimplicity.com](afs) was exactly what was in the ```/docs``` folder when I'd set almost everything up in the root from the tutorials. Thus, missing the ```_includes``` for the navigation bar and various other things.  

Possibly, I shouldn't have included the author thing above, because I see that the poor little ```by``` line is still devoid of my handle, but I'll tackle that eventually, probably after I decide which direction I'm really going, probably root I think, and straighten out my gnarled code so things work the way I expect. But for tonight it's bedtime again.  

[page-types]: https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#:~:text=for%20your%20organization.%22-,Types%20of%20GitHub%20Pages%20sites,-There%20are%20three
[jekyll-docs]: https://jekyllrb.com/docs/home
[afs]: https://assumeforsimplicity.com
