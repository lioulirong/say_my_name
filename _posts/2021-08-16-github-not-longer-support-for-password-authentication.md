---
layout: post
title:  "Github 從2021八月13號後不再支援password authentication!"
date:   2021-08-16 10:20:31 +0800
categories: github
---
### 使用HTTPS protocol operation

你要push東西上去Github結果看到

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
```
![](https://c.tenor.com/t440UaMq4KkAAAAC/doge-doge-on-doge.gif)

過去如果你有一個本地端的Git repository，除了透過[SSH key](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)認證你還有一個選項可以做Github operation(例如 :push, pull, clone 等等)—用password authentication，簡單來說就輸入你的Github 帳密來操作。

```sh
Username for 'https://github.com': 
Password for 'https://shreyas-jadhav@github.com': 
```

關於這個消息Github去年(2020)底就已經於[他們部落格文章](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)預告在先，不過其實這不代表你從此以後就必須改用SSH連線，你還是可以照樣使用https protocol來操作。只是你必須使用PAT(personal access token)。

### 使用Personal Access Token

#### 產生PAT

到你的github page->`setting`->`developer setting`->`personal access token`->`generate new token`，你會拿到一組token，他的長相大概像ghp_ruMtcHYALGneWiGRK7Imj3MmlUeH6o1ylbHa這樣。

#### 1. 把token當作密碼使用

```sh
git push
Username for 'https://github.com': 
Password for 'https://shreyas-jadhav@github.com': <輸入你的token>
```

#### 2. 把token放在remote url裡面

```sh
git remote -set-url origin https://<token>@github.com/<git_url>
```

這種方法相當不建議，因為你把token用明碼的方式存起來。

#### 3. (For Windows) 使用認證管理員([Windows credential](https://support.microsoft.com/en-us/windows/accessing-credential-manager-1b5c916a-6a16-889f-8581-fc16e8165ac0))
![](https://support.content.office.net/en-us/media/7a91c53e-f719-4762-830c-af9d7723de3a.png)
**控制台**的**認證管理員** => **Windows 認證** => 找到/或自行新增 `git:https://github.com` => **編輯** => 在密碼欄加上 **Github Personal Access Token** => 完成!

### 那裡適合存放Personal access token?

一個PAT在你的page上產生之後，他只會顯示一次，如果你沒有存起來或記起來(不實際)，就永遠丟失了。不太建議用純文字存在記事本不。可以：

#### 1. 使用windows 認證管理員 (前面示範過的第3個方法)
#### 2. 使用Git 的credential helper
```sh
git config --global credential.helper manager
```

之後你只要輸入一次使用者名稱、PAT，PAT就會被很安全的存放起來

### 參考

[Support for password authentication was removed](https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to)

[Password authentication is temporarily disabled as part of a brownout. Please use a personal access token instead](https://stackoverflow.com/questions/68191392/password-authentication-is-temporarily-disabled-as-part-of-a-brownout-please-us/68192528#68192528)

[Where to store the personal access token from GitHub?](https://stackoverflow.com/questions/46645843/where-to-store-the-personal-access-token-from-github)

[git-credential-store - Helper to store credentials on disk ](https://git-scm.com/docs/git-credential-store)

[gitcredentials - Providing usernames and passwords to Git ](https://git-scm.com/docs/gitcredentials)
