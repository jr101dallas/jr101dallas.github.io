---
layout: post
title:  "Blogging On Vacation"
date:   2021-09-22 22:59:53 -0600
---

# Looking Ahead
One thing I'm trying to pay attention to and to look ahead and build a strategy for is the holidays! I definitely don't want to end up chained to my computer if I manage to either have family in town for the holidays or go visit people. Vacations have to happen sometime!!!

Not to mention the fact that sometimes life just gets busy.... I did start in this direction back in January and then just didn't get back to it until September. That won't work for blog publishing, seo, and adoring fans though.

## Jekyll and GitHub Pages
I thought that I was going to be relying on the Jekyll functionality around blog posts coupled with the fact that GitHub pages uses Jekyll as its base. However, I think I'm finding that might not work. I found something from [Sangsoo Nam](sangsoo-nam) that indicates that there has to be a commit to trigger the page update. Working in deployment/delivery pipelines a lot and thinking about infrastructure as code, I started wondering if GitHub actions would have an easy solution, and  when I read through this [Jekyll issue](jekyll-issue) and the link at the end that points to [netlify](netlify) I think I have a good shot at figuring out the way forward. I'll dig a bit on it to find out, but it seems like maybe the default build trigger on GitHub pages might just be commit and if there's a way to just reference the existing workflow in ```.github/workflows/main.yml``` and add a nightly timed trigger then I'd have everything I need.

[sangsoo-nam](http://sangsoonam.github.io/2018/12/27/writing-upcoming-posts-in-github-pages.html#:~:text=only%20when%20there%20is%20a%20new%20commit)
[jekyll-issue](https://github.com/jekyll/jekyll/issues/6536#:~:text=till%20a%20new%20build%20is%20triggered)
[netlify](https://answers.netlify.com/t/scheduling-builds-and-deploys-with-netlify/2563/9#:~:text=easy%20to%20use-,GitHub%20Actions,to%20schedule%20builds,-.)