---
layout: post
title:  "Pretty Posts"
date:   2021-10-01 09:11:34 +0000
category: website
tags: jekyll
---

# Jekyll Example
For my Posts [page](/posts/) I've just been using the basic example from the Jekyll [documentation][doc]. It's been a good start since it let me see when things are being published easily. But.. I think I'd like to show the dates, and I might want to see about breaking them up into groups.  

## Showing Dates
It seems like showing the date should be pretty easy since I know the Liquid and templating should just show ```post.date``` like it shows ```post.title``` but I'm betting I'm going to want different formatting.  

Yeah, a minor change and a ```bundle exec jekyll serve --future``` later and definitely the format is a problem. It looks like this, ```2021-09-16 11:18:09 -0500 Reorganizing```, so I'm going to need to do some research.  

## Date Format Solution
I found a [page][date-format] with what I needed for the date formatting at least. With some playing around with the formatting I finally got what I wanted.  

## Grouping
Unfortunately, I've also run out of time and I'll have to takle the grouping by month later.  

[doc]: https://jekyllrb.com/docs/posts/#:~:text=Creating%20an-,index%20of%20posts,-on%20another%20page
[date-format]: https://help.looker.com/hc/en-us/articles/360023800253-Easy-Date-Formatting-with-Liquid
