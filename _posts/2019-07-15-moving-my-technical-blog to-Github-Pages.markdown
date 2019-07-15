---
title:  "Moving my technical writings to Github Pages!"
comments: true
date:   2019-07-14 12:31:23
categories: [general]
tags: [announcements]
---
One of my good friends said to me that I should start using Git Hub pages to present the rest of my technical blog writings as a pose to Wordpress. I was initially against the idea as I really liked the LAMP blogs. However I had seen that there were other people that were using Github Pages I had to see what the fanfare was about.  So I have decided to move to using it for the foreseeable future. I can move back to Wordpress if I choose to. But for now I really like it because it suits my workflow of writing  than writing in a browser.

## Observations 
Github is actually using Jekyll as the blog system. Jekyll is written in Ruby and is termed as a static generator blogging system.  This is very much different from Wordpress where you need a database to store information  and WordPress will provide the ui and such. In Jekyll you need to use structure your blog in a certain file structure
Here is an example of a Jekyll web site.(in this case 1 level deep).
```
C:\Users\weiyentan\Documents\GitHub\weiyentan.github.io
├──css
├──fonts
├──images
├──js
├──_drafts
├──_includes
├──_layouts
├──_posts
├──_sass
├──.gitignore
├──categories.md
├──feed.xml
├──Gemfile
├──Gemfile.lock
├──humans.txt
├──index.html
├──LICENSE
├──README.md
├──screenshot.png
├──sitemap.xml
├──tags.md
└──_config.yml
```

## Gotchas
This particular layout is unique to this website because its based on the jekyll-uno theme. In order for me to use a particular theme I found that the best way to do it is to clone the repository and copy that over. In the next post I will explain the steps that I took 