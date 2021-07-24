---
title: "從C源碼到Assembly "
date : 2021-07-24 10:16:00 +0800
layout : "post"
author : "leolijung"
---



在[gcc 的 auto vectorization ：加速你的loop迴圈](https://maenjoe.github.io/say_my_name/gccautovectorizationaccelerateyourloop)這篇文中提到使用編譯器優化選項`-O3`，編譯後可以檢視輸出的組合語言，來看看哪些確實做了哪些優化，哪些東西沒有優化

```shell
#output assembly
clang -S sourcefile.c
```

但有個問題，通常需要訓練有素的高級打字員才有辦法閱讀組合語言，這裡分享一些高質量的資源來識讀組合語言：

### Comparing C to machine language

這是一個很哈扣的油土伯-[Ben Easter](https://www.youtube.com/channel/UCS0N5baNlQWJCUrhCEo8WlA)的教學，他的頻道專門創客自幹各種計算機以及計算機的小伙伴

<iframe width="560" height="315" src="https://www.youtube.com/embed/yOyaJXpAYZQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

如果你現在上班不能把影片播出來，且可看我斷章取義如下。

影片中先寫了一個費氏數列的C程式，編譯成machine code再用disassembler反組譯成Assembly，再將C源碼與Assembly兩相比較。值得注意的是，像Ben Easter在解釋的過程中也跳過很多Assembly不解釋，因為他也看不懂，看來讀Assembly其實是不必太鑽牛角尖的，編譯器本來就會產生很多非本意（intention）的Assembly。

費氏數列的C程式碼：

```c
//Fibonacci Series C program: fib.cc
#include <stdio.h>

int main (void) 
{
    int x,y,z;
    while(true)
    { 
        x = 0;
        y = 1;
        do
        {
            printf("%d\n",x);
            z = x + y;
            x = y;
            y = z;
        }while(x < 255);
    }
    return 0;
}

```
執行
```bash
clang -S fib.cc
```
得到`fib.s`
```shell
main:                                   # @main
    .cfi_startproc
# %bb.0:
    pushq   %rbp
    .cfi_def_cfa_offset 16
    .cfi_offset %rbp, -16 
    movq    %rsp, %rbp
    .cfi_def_cfa_register %rbp
    subq    $32, %rsp
    movl    $0, -4(%rbp)
.LBB0_1:                                # =>This Loop Header: Depth=1
                                        #     Child Loop BB0_2 Depth 2
    movl    $0, -8(%rbp)
    movl    $1, -12(%rbp)
.LBB0_2:                                #   Parent Loop BB0_1 Depth=1
                                        # =>  This Inner Loop Header: Depth=2
    movabsq $.L.str, %rdi
    movl    -8(%rbp), %esi
    movb    $0, %al 
    callq   printf
    movl    -8(%rbp), %esi
    addl    -12(%rbp), %esi
    movl    %esi, -16(%rbp)
    movl    -12(%rbp), %esi
    movl    %esi, -8(%rbp)
    movl    -16(%rbp), %esi
    movl    %esi, -12(%rbp)
    movl    %eax, -20(%rbp)         # 4-byte Spill
# %bb.3:                                #   in Loop: Header=BB0_2 Depth=2
    cmpl    $255, -8(%rbp)
    jl  .LBB0_2
# %bb.4:                                #   in Loop: Header=BB0_1 Depth=1
    jmp .LBB0_1

```

把與本意不相關的行數刪掉後，一切看起來就清楚多了：
```shell
main:                                   # @main
.LBB0_1:                                # 外層while回圈開始
                                        # 
    movl    $0, -8(%rbp)                # x = 0;
    movl    $1, -12(%rbp)               # y = 0;
.LBB0_2:                                # 內層while回圈開始
#-------------------------------------------------------------------------                                         
    movl    -8(%rbp), %esi              # 為printf做準備
    movb    $0, %al                     # 為printf做準備
    callq   printf                      #                   printf("%d\n",x);
#-------------------------------------------------------------------------
    movl    -8(%rbp), %esi              # 把x放進暫存器%esi
    addl    -12(%rbp), %esi             # 把y與暫存器%esi
    movl    %esi, -16(%rbp)             # 相加結果放進z		z = x + y;
#-------------------------------------------------------------------------
    movl    -12(%rbp), %esi             #把y放進暫存器
    movl    %esi, -8(%rbp)              #暫存器的值給x        x = y;
#-------------------------------------------------------------------------    
    movl    -16(%rbp), %esi             #把z放進暫存器
    movl    %esi, -12(%rbp)             #暫存器的值給y        y = z;
#-------------------------------------------------------------------------    
    movl    %eax, -20(%rbp)             #
# %bb.3:                                #   in Loop: Header=BB0_2 Depth=2
    cmpl    $255, -8(%rbp)              # 比較 x < 255
    jl  .LBB0_2                         # 如果比較小（less）就跳到內層while回圈開始
                                        # while(x < 255);
#-------------------------------------------------------------------------       
# %bb.4:                                #   in Loop: Header=BB0_1 Depth=1
    jmp .LBB0_1                         # while(true)（LBB0_1外while回圈開始）


```

看到這裡，大概也把Ben Easter解釋的差不多說完了，關於`fib.s`裏面還有很多有去的細節，例如暫存器的部份，還有很多很棒的資源可以參考。

MIT的這堂開放課程解釋了x86-64的一些歷史(legacy)和設計，歷史也不完全是歷史，現在很多都還在使用，但是要分清楚哪些是設計哪些是歷史，否則你會很疑惑。這堂課也有提供簡報檔，可參考[這裡](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-172-performance-engineering-of-software-systems-fall-2018/lecture-slides/MIT6_172F18_lec4.pdf)

<iframe width="560" height="315" src="https://www.youtube.com/embed/L1ung0wil9Y?start=792" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

