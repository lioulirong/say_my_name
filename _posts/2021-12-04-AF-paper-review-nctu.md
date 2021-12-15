---
layout: post
title: Auto Focus paper review (1)
date: 2021-11-22 10:20:31 +0800
author : "leolijung"
categories: 
---

### 緣起

參考交大電子所2006年杭學鳴老師指導的學生林耀仚撰寫的畢業論文[On Dynamic Auto-Focusing Algorithms](https://ir.nctu.edu.tw/handle/11536/80623)，年代有久遠，但接著參考Cornell和Google research在2020年發表的另一篇[Learning to Autofocus](https://openaccess.thecvf.com/content_CVPR_2020/papers/Herrmann_Learning_to_Autofocus_CVPR_2020_paper.pdf) (Machine Learning 應用在AF algorithm ) 相當可以了解Auto Focus(AF)入門以及AF的演進。

### AF演變

Auto Focus 是image pre- prossessing的一種技術，相對於image post-prossessing，像我研究所的論文做的影像處裡就是屬於 post-prossessing。

Auto Focus Algorithm 協助相機拍攝時自動找到適合的focus position。早期1980s會使用**紅外線**來量測距離、還有使用光線明暗的被動式自動調調焦，各自都有一些限制。比如太暗的場景，或是反射效果不好的表面，AF表現就不好，**後來發展出利用相機本身影像資訊來做AF的演算法**。

本文主要講的是這種演算法。因為這個演算法會計算影像的contrast，所以又稱為 **contract based** 的AF，近年發展出一種感光元件 **dual-pixel sensor**，可以開發 **phase based AF**。這種感光元件在手機和DSLR相機上越發常見了[2]。

### CCD/CMOS sensor

CCD: charged-coupled device

CMOS:**C**omplementary **M**etal-**O**xide-**S**emiconductor


|  | 優點  |缺點|
| - | ---- |-|
| CCD | 低雜訊<br>高敏感 |高耗電<br>貴|
| CMOS | 便宜<br>SoC implementation |高雜訊<br>複雜電路|

比較引起我注意的部分是CMOS可以有 SoC implementation[1]。

### Auto Focusing ABC

(contrast-based)AF的三本柱

1. Focus value metric
2. Windows selection
2. Search algorithm

#### Focus value metric

Focus value metric 可以說是一種量化影像的高頻訊號強度的方法，越清晰(越靠近focus position)，高頻訊號應該越強。

Focus value metric 應該滿足三個特性：

> 1. its process should be independent of the received data and its measurement is
   universal for all types of image structures. 
   2. It should be a decreasing function with respect to the true focus of the target. If the image is sharper, the focus value gets higher.
   3.  It can tolerate disturbance and noise. Even

[1]提供了三個選擇：

1. Sobel Operator(一階)
2. Laplacian of Gaussian
3. Wavelet Transform

##### Sobel Operator 

一階梯度運算。A是影像矩陣。

![](https://i.imgur.com/iPFuzwh.png)
![](https://i.imgur.com/sGVDIJi.png)

##### Laplacian of Gaussian

laplace 是二階梯度，遮罩長得像下面那樣，有兩種選擇。

![](https://i.imgur.com/smZDcvz.png)

因為二階遇到noise會有impulse，所以再套上一個高斯函數。

![](https://i.imgur.com/ZMTqDy7.png)

![](https://i.imgur.com/mYVcCyC.png)

##### Wavelet Transform

上面兩個都是空間域的作法，Wavelet Transform則是頻率域的作法。我想如果小波轉換可以，那或許也可以使用傅立葉轉換。

現在回到focus value metric的部分，上述的三個方法都可以把影像的清晰程度量化成數值，一個好的focus value metric應該可以將影像stack畫成如下圖形成一個focus curve。(影像的stack是指一組在不同的focus postion拍攝形成的影像集合)。

**Focus value數值最高的地方對應的Focus position也就是AF演算法要找的答案**。

![](https://i.imgur.com/c9NFLWW.png)


#### Window selection
顧名思義就是將一張影像分成幾個區域，並不是每個區域的價值都相同，以手持式相機來說，靠近中央的window 通常其實就是影像最重點的的地方，或者也可讓使用者來指定對焦的區域而選擇對應的window。

在[2]中直接是把某個widow當成"patch"，也就是完全忽略patch以外的影像，只專注在patch本身的focus value。

在[1]中則是將每個區域給予權重計算一整張影像的focus value:

![](https://i.imgur.com/jVg4417.png)

> The overall focus value is a weighted sum of those window value
>
> In general, the center windows have higher weights because most people put their attention on the center of an image.
>
> Another approach is that the window with the maximum focus value has the highest weight or hooses it as the focus window. The overall focus value comes from the focus window only.



![](https://i.imgur.com/Xr5w7eC.png)

![](https://i.imgur.com/hHk3QLy.png)
不同顏色的曲線表示不同的window的focus curve

#### Search algorithm

相機一開始處在初始的focus postion，AF search algorithm 目的要找到focus value的argmax也就是焦點，最理想的focus postion輸出響應下圖：往正確的方向移動focus position ，最後固定在焦點。

但這並不可能這麼完美，首先控制基本上都會有overshoot，同時control loop也會有delay，再來是我們根本不知道我們是否已經找到focus value最大值，自然不知道要再什麼地方停下來。

![](https://i.imgur.com/GrGN5mT.png)

##### Full search algortihm

最簡單(naive)的search algorithm，就是把所有的focus position 都走過一遍，自然就知道焦點在何處。

![](https://i.imgur.com/p4wbhQK.png)

##### climbing search algortim

利用不同focus postion區域focus value變化會展現不同特性來判斷停止的地點。顯然和full search 相比可以省下後半部的時間。

climbing search 的缺點是，若遇到local maximum，這個演算法就會失效。

![](https://i.imgur.com/CCNjnHS.png)

![](https://i.imgur.com/NWl07De.png)

### 結語

[1]的文章畢竟是在2006年發表，科技發展日新月異，plenoptic camera的出現，完全顛覆原本contrast-based的方法，而在search algoritm的部分也可以透過machime learning 來解決，讀過[1]之後相當推薦閱讀[2]這篇文章，對於AF技術的認識非常有幫助。

### 參考資料
[1] 林耀仚,2006,"**On Dynamic Auto Focus Algorithm**",NCTU,https://ir.nctu.edu.tw/handle/11536/80623
[2] Charles Herrmann et al.,2020,"**Learning to Autofocus**",Cornell Tech&Google Research,https://openaccess.thecvf.com/content_CVPR_2020/papers/Herrmann_Learning_to_Autofocus_CVPR_2020_paper.pdf
