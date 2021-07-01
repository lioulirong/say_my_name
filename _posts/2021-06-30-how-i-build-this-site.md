---
title: "Jekyll : How I build this Blog"
date : 2021-06-30 21:16:00 +0800
layout : "post"
author : "leolijung"
---
I recommend you to use Jekyll on Linux instead of on Windows.

Jekyll is a static website generator. You can find enough introduction to it on the Internet.
I'll skip that part.

The same applied to the installation.
As for the tutorial for Jekyll it self, I recommend [Mike Dane's tutorial](https://www.youtube.com/watch?v=T1itpPvFWHI&list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB&index=1) on YouTube.
I will cover the content of the video in my future article.

To Set up Github Page, you can refer to [GitHub docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll).

1. Create a repository on Github and clone to your local machine, then (on your local computer)
```bash
jekyll new <blogname>
```
2. specift the `baseurl` value as "\\<your-project-name>"
```yaml
baseurl: "/say_my_name" # the subpath of your site, e.g. /blog
```
3. to preview the site on your local machine
```bash
bundle exec jekyll serve
```
Any change of the markdown files will reflect on local host web immediately.
The \_config.yaml changes, however, require you to restart the server for some technical reason.
4. push to Github
```bash
git push
```
5. find the *url* in your project setting->github pages


