---
layout: post
title:  "CNAME Record"
date:   2021-10-07 09:11:34 +0000
category: website
tags: workflow actions pages cname
---

# Limited Time
I've been a bit frustrated with the struggle to get my CI/CD pipeline working properly, not good for morale and momentum. Eventually, I'm going to write up a good concise how-to for what I've done so that others hopefully won't end up with the same problems. And now... ```assumeforsimplicity.com``` keeps breaking. 404s on the home page that are throwing me off.  

## Looking Around
I've noticed that the GitHub Pages setting for Custom Domain isn't sticking properly. I don't think it was complaining or resetting before when my source folder was just set to the root of the ```main``` branch but I haven't gone back to experiment yet. When I originally set it up, GitHub helped me create the ```CNAME``` file in the root of my repo and I see that file is being propagated to my ```gh-pages``` branch by my GitHub Action workflow Jekyll Action. So that seems right. But, when I keep adding back my domain in the custom domain setting I was seeing a little whirl around the GitHub icon next to the setting. I didn't pay a lot of attention to it before, but as I came to see that things were actually broken, it got my attention.  

UPDATE: The CNAME file wasn't being propagated. It was being created new every time I reset the Custom Domain setting.  

## Scroll Down and Wait
After a little more looking, and scrolling down and waiting, which I didn't do before, I saw that GitHub was actually verifying, or trying to verify, my domain. Good.... But it was failing. After actually closely reading the message, it seemed to be saying I needed a ```CNAME``` record. That's when I confirmed the stuff about the CNAME file above. Nope, it must not mean that. Well.... I went looking back at my domain registration on Google Domains. I knew there was a CNAME record there, but maybe. And, what I found was that was a CNAME that has to be for the internal lookup at Google. After some trial and error, I added another CNAME record for ```*.assumeforsimplicity.com``` to point back at ```jr101dallas.github.io```. When I went back to the GitHub Pages settings again to set the Custom Domain value again, the DNS check finally came up as a green checkmark. Finally. Now, I think, I'm finally back in a momentum and good morale position. Thank goodness.  

ALSO UPDATE: GitHub recommends AGAINST the wildcard record in your domain configuration. It will allow anyone on GitHub to put up their stuff at your domain, or something like that. I saw that I'd done something wrong and undid it. The real answer is [here](/website/2021/10/09/oh-duh.html).