---
title: "7月更新-2：gdb, auto-vectorize, vim插件, open source"
date : 2021-07-07 10:16:00 +0800
layout : "post"
author : "leolijung"

---

近期看到資源、新聞、題材

1. [CMU的GDB tutorial](http://www.cs.cmu.edu/~gilpin/tutorial/)

   最最最基礎的簡單使用可以參考 StackOverflow上的這個問題[Debug C++ program in Linux](https://stackoverflow.com/questions/370622/debug-c-program-in-linux)

   > You can use [`gdb`](http://www.gnu.org/software/gdb/) for this:
   >
   > ```bash
   > $ gdb hello # hello is the program name
   > ```
   > This will start gdb and prompt you for what to do next. The next command executes one line of source and stops at the next line.
   
   下面有網友貼心提醒
   
   > Don't forget to compile your source code using -g option. Like this: `g++ -g helloWorld.cc` This is going to create an a.out executable file. You'll be able to debug your a.out exe using `gdb ./a.out` command. Another tool you may use it's [ddd](http://www.gnu.org/software/ddd/) basically a GUI for gdb.
   
   接著就可以開始好好欣賞[CMU的GDB tutorial](http://www.cs.cmu.edu/~gilpin/tutorial/)：
   
   文章拿一個實作的很荒謬的Linked list [sample code](http://www.cs.cmu.edu/~gilpin/tutorial/main.cc)（想讀code的話，先看他的main就好）來示範，裏面有一個bug 會導致segmentation fault，文章使用了幾個技巧來debug:
   
   1. `backtrace` ：首先 segmentation fault發生，使用backtrace看看誰呼叫了最後一個function及傳入function的引數，
   2. `x` (exame value in a certain adress) ： 用`x`看傳入引數的值 （本例子 該引數為x0001）
   3. conditional break point：當傳入引數的值為x0001時，break
   4. `step`：一步一步看（hint:不用一直輸入step，只要按enter，就可以重複上一個命令）
   
   作者的結語
   
   > This document only covers the bare minimum number of commands necessary to get started using `gdb`
   
   簡單的程式，知道這些，足矣。
   
2. [Auto-vectorization with gcc 4.7](http://locklessinc.com/articles/vectorize/)

   如果你的程式有非常多非常多的loop，你也許會想用vectorization來加速，一種SIMD的平行化方法。

3. [每個開發者都應該要會用的編輯器–Vim](https://medium.com/@jinghua.shih/%E6%AF%8F%E5%80%8B%E9%96%8B%E7%99%BC%E8%80%85%E9%83%BD%E6%87%89%E8%A9%B2%E8%A6%81%E6%9C%83%E7%94%A8%E7%9A%84%E7%B7%A8%E8%BC%AF%E5%99%A8-vim-5f83349973a3)

   [施靜樺](https://medium.com/@jinghua.shih)寫的Medium文章，這篇沒有教你怎麼用VIM，**他教你怎麼學VIM**，**VIM的環境設定（vimrc**），同時也推荐不少**不錯的VIM plugin**，推荐的plugin大致上就是很多部落格或網站的交集經典版，**Vundle 套件管理**是一個玩plugin不錯的敲門磚。

   關於學習Vim本身他分享了一些資源：

   >  [Vim 的哲學](https://segmentfault.com/a/1190000000445598)
   >  這是一個系列，事實上是寫這篇文章時偶然發現的，寫得真好。裡面除了教你從頭開始熟悉 Vim 之外，也有很多很棒的分享，就算不是 vim 新手一定也會有收穫的。
   >
   >  [Learn Vim for the Last Time](https://danielmiessler.com/study/vim/)
   >  滿長的，但是是滿好的入門教材。
   >
   >  [Vim Fandom](https://vim.fandom.com/)
   >  基本上這個不太需要介紹，因為你只要常用 google 搜 vim 相關的資源就會常常看到這個網站。總之這是一個算是滿厲害的論壇吧，基本上帶著問題進來絕對不會空手而返的。
   >
   >  [Vim Galore](https://github.com/mhinz/vim-galore)
   >
   >  一個非常詳細，從頭開始帶你暸解 vim 的指引。很厲害也很實用。

   最後他的一個小結語命中我的紅心

   > **最常使用到 vim 的是拿來寫簡單的 C 跟 markdown files**，
   >
   > 寫 Ruby 的話我會用 Rubymine 或是 Sublime Text，所以基本上沒有裝什麼 language-specific 的插件

   我玩了Vim一陣子，發現如果專案稍微長大一點點，Vim就顯得實在是不好用：沒辦法立刻跳到函式定義，跨文件尋找關鍵字沒有Sublime好用，瀏覽文件樹稍嫌麻煩。非常好奇，Vim的重度使用者，到底都拿Vim寫些什麼。

   我個人覺得Vim最好的使用時機就是，你很明確知道你要寫什麼，要寫在哪裡，寫在哪些檔案，需要調用哪些資原，所以可能小專案或是你很熟悉的專案比較適合使用Vim作為主要的編輯工具。

4. [20+ Open Source Project for Beginners](https://dev.to/surajondev/beginners-guide-to-starting-your-open-source-journey-1bgb)

   這裡的beginner不是程式初學者，是開源專案初學者，列出的專案都是屌炸天專案

   會有人寫這種文章本身倒是很有趣。