---
title: "在Windows使用gVIM plugin"
date : 2021-07-05 21:15:00 +0800
layout : "post"
author : "leolijung"
---



在Lunix上使用VIM的老玩家應該都知道設定vim就是在~/.vimrc寫vimscript，想安裝plugin就是把插件的檔案放到~/.vim資料夾就是了。

在Window上也是大同小異，就是`~/.vimrc` `~/.vim`檔案位置稍微不同，把Linux的home`~`變成`C:/Users/<username>`

(**如果沒有.vimrc文檔或.vim資料夾，自己創建便是**)



![](https://i.imgur.com/Q3Zo9nF.png)

我在Linux上的插件管理是用Vundle，經過測試，同樣的.vimrc和插件在windows gVIM上都可以正常安裝使用，範例可以參考 [我的vimrc配置](https://github.com/MaEnJoe/VImrcGoodForYou)，目前放在GitHub上面慢慢調教中，終極目標可以一鍵把你的vim設定成不輸sublime或vscode的IDE (至少不要屌輸)。

[我的vimrc配置](https://github.com/MaEnJoe/VImrcGoodForYou)目前包含的插件：

1.   [Vundle](https://github.com/VundleVim/Vundle.vim) package manager
2.  [The NERDTree](https://github.com/preservim/nerdtree) 
3.  [The NERDCommenter](https://github.com/preservim/nerdcommenter) 
4. 我的 coustum function
   1. F9 mirror NERDTree to all tab
   2. auto save current session as .leo.vim
   3. open NERDTree when new buffer entered

![](https://i.imgur.com/9Wbu8BV.png)