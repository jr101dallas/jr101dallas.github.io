---
layout: post
title:  "Actions Rewrite"
date:   2021-10-05 09:11:34 +0000
---

# Borked
Something still isn't right with my GitHub Actions to build and publish my GitHub Pages site on a schedule. I've got a [page][jekyll-action] to look at but it's not all the way consistent or clear at the moment.  

## Confusing
At the time of this writing, the page refers to both ```main``` and ```master``` branches, but then it also talks like the real branch is ```gh-pages```. I've just set up the action for now as best as I can guess with the previous schedule in it to see what happens.  

## Huh?
And it's creating a ```master``` branch and not actually publishing to the assumeforsimplicity.com domain. I'm definitely lost somewhere. Going to have to do some actual research and trial and error.  

[jekyll-action]: https://jekyllrb.com/docs/continuous-integration/github-actions/
