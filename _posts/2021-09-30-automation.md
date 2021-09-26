---
layout: post
title:  "Automation"
date:   2021-09-30 11:29:59 -0600
---

# GitHub Actions
It's time to see if I can get the code running that [Sean Killeen](sean-killeen) posted about. I'm thinking that I'm about to have a pause in my regular time blocks that I've been using on this project, so having the actions running regularly to publish the formerly future posts would be advantageous.

[sean-killeen](https://seankilleen.com/2020/02/how-to-deploy-github-pages-on-a-schedule-to-publish-future-posts/)

## So...actually
When I save myself a breadcrumb like this, I know that a solution exists, and I just keep working on whatever I'm busy with until something actually catches fire. Being that I now have some smoke, I went back to the solution I knew existed and took a look. Like frequently happens, I've had some time to think and I took another shot at searching. I found some interesting things.  

1. GitHub Actions [documentation](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#:~:text=trigger%20a%20workflow%20at%20a%20scheduled%20time)  
1. a GitHub Pages [Action](https://github.com/marketplace/actions/github-pages-action)  
1. and a suggested workflow for Jekyll when I clicked into my repository Actions tab  

## Adding Two and Two Hopefully
Where I've ended up for the moment is this. The recommended Jekyll GitHub Action, plus the ```schedule``` example from the GitHubActions documentation, to get the following contents in my ```jekyll.yml``` in the root of my repository. I'll test it over the next couple of days by only commiting locally and not pushing to GitHub. If it's working then I should see assumeforsimplicity.com get updated anyway after 11:30 AM whatever time the servers run on.

``` yaml
name: Jekyll site CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron:  '30 11 * * *'

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Build the site in the jekyll/builder container
      run: |
        docker run \
        -v ${{ github.workspace }}:/srv/jekyll -v ${{ github.workspace }}/_site:/srv/jekyll/_site \
        jekyll/builder:latest /bin/bash -c "chmod -R 777 /srv/jekyll && jekyll build --future"
```