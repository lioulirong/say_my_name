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

Setting up Github Page, you can refer to [GitHub docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll).

Create a repository on Github and clone to your local machine, then
```bash
jekyll new <blogname>
```

to preview the site on your local machine
```bash
bundle exec jekyll serve
```

Any change of the markdown files will reflect on local host web immediately.
The \_config.yaml changes, however, require you to restart the server.

