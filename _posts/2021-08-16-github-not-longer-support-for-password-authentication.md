---
layout: post
title:  "Github 從2021八月13號後不再支援password authentication!"
date:   2021-08-16 10:20:31 +0800
categories: github
---
過去如果你有一個本地端的Git repository，除了透過[SSH key](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)認證你還有一個選項可以做Github operation(例如 :push, pull, clone 等等)—用password authentication，簡單來說就輸入你的Github 帳密來操作。

```bash
Username for 'https://github.com': 
Password for 'https://shreyas-jadhav@github.com': 
```

關於這個消息Github去年(2020)底就已經於[他們部落格文章](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)預告在先，不過其實這不代表你從此以後就必須改用SSH連線，你還是可以照樣使用https來操作。



[Support for password authentication was removed](https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to)