---
title: "gcc 的 auto vectorization ：加速你的loop迴圈 "
date : 2021-07-12 10:16:00 +0800
layout : "post"
author : "leolijung"
permalink: /gccautovectorizationaccelerateyourloop
---

[TOC]

### 使用CPU指令集平行化 - SIMD（single instruction multiple data）

簡單來說就是，有一段像這樣的程式碼，我們要在**完全不修改原始碼**的情況下讓他加速，我們要加上一些編譯器選項，讓編譯器幫你用SIMD指集及**平行化** （而不是OpenMP那種高階的平行化）

```c
//example from https://gcc.gnu.org/projects/tree-ssa/vectorization.html
//foo.cc
int a[256], b[256], c[256];
foo () {
  int i;

  for (i=0; i<256; i++){
    a[i] = b[i] + c[i];
  }
}
```


### 預備：先檢查SEE和AVX2指令集
SSE和AVX2是Intel的SIMD指令集，他和硬體（你的CPU）有關，

1. 你需要確認你的CPU是否支援SSE/AVX

```bash
$ cat /proc/cpuinfo  | egrep "(flags|model name|vendor)" | sort | uniq -c
```
2. 你需要確認你的gcc/g++ compiler 是否有支援AVX2

```bash
$ gcc -march=native -dM -E - < /dev/null | egrep "SSE|AVX" | sort 
```
其實很高的機會你的PC應該隨隨便便都有支援SSE/AVX。

### gcc 的 auto vectorization 編譯器選項

```makefile
gcc -ftree-vertorize foo.cc -fopt-info-vec-optimized
```

`-ftree-vertorize`告訴gcc你想要vertorize

`-fopt-info-vec-optimized`讓gcc印出向量化等的細節（讓你知道是否成功，如果失敗，那又是為什麼）

幸運的話，編譯後會出現像下面的訊息

```bash
foo.cc:7:11: note: loop vectorized
```



其實編譯器選項 使用 `-O3` level3的最佳化，也是可以達到同樣的目的，向量化是包含在裡面的。

根據Amdhal's law，你的迴圈如果wall time 佔比很高，那你愈可以感覺到加速的效果。

關於迴圈的加速還有`-funroll-loops`（減少branch 的數量）。

### 什麼樣的程式碼可以自動向量化（ auto vectorization）
GCC的 auto vectorize project

GCC compiler既聰明，又沒有那麼聰明，常常一些程式碼是不行auto vectorizatio，或是auto vectorizatio 也不是很完全，你可以使用

```bash
g++ -S foo.cc
```

來看看編譯器幫你產生了什麼組語。

在GNU GCC的官方文件[Auto-vectorization in GCC](https://gcc.gnu.org/projects/tree-ssa/vectorization.html#Example)的裏面，與其告訴你規則，他直接給了很多範例讓你知道誰可以被向量化，例如

```c
//Example 5:
struct a {
  int ca[N];
} s;
for (i = 0; i < N; i++)
  {
    /* feature: support for alignable struct access  */
    s.ca[i] = 5;
  }
```

你可以按照你的需求再裏面找找看，不過這份文件有點久遠了，更多的case可能現在都支援了。

#### 進階一點

loop通常是操作array，於是要考慮

1. alignment
2. pointer alias

用[lockless 文章](https://locklessinc.com/articles/vectorize/)的這個程式碼來思考

```c
void test4(double * __restrict__ a, double * __restrict__ b)
{
	int i;

	double *x = __builtin_assume_aligned(a, 16);
	double *y = __builtin_assume_aligned(b, 16);

	for (i = 0; i < SIZE; i++)
	{
		x[i] += y[i];
	}
}
```

`__builtin_assume_aligned`告訴編譯器記憶體alignment

`__restrict__`告訴編譯器指標沒有alias

大概就是這兩招打天下，剩下就是loop body裡面的敘述，同樣意思但不同的描述，會影響是否向量化，例如

```c
/* max() */
if (y[i] > x[i]) x[i] = y[i];//FAIL! gcc isn't allowed to introduce extra writes to a memory location.
```

```c
/* max() */
x[i] = ((y[i] > x[i]) ? y[i] : x[i]);//OK! with: maxpd  (%rdi,%rax,1),%xmm0
```

### 測試speedup

`main.cpp`:給vec_loop和nloop兩個function計時

`vec_loop.cpp`:定義vec_loop函數

`nloop.cpp`:定義nloop函數

main.cpp

```c++
#include "CycleTimer.h"
//#include <bits/stdc++.h> //All main STD libraries
#define ITER 1ull<<21
#define LEN 1024

int vec_loop(int * __restrict__ a,int* __restrict__ b,int* __restrict__ c,unsigned,unsigned long long);
int nloop(int * __restrict__ a,int* __restrict__ b,int* __restrict__ c,unsigned,unsigned long long);

void initValue(int *a,int *b,int *c,unsigned);

int main()
{
    int a[LEN], b[LEN], c[LEN];
    double start=.0, end=.0, refTime=.0,refTime2=.0;
    int result1 = 0,result2 = 0;    

    initValue(a,b,c,LEN);

    start = CycleTimer::currentSeconds();
    result1 += vec_loop(a,b,c,LEN,ITER);//vectorize loop
    end = CycleTimer::currentSeconds();
    refTime = end-start;
    
    start = CycleTimer::currentSeconds();
    result2 += nloop(a,b,c,LEN,ITER);//normal loop
    end = CycleTimer::currentSeconds();
    refTime2 = end-start;
    
    printf("time elapsed: %2.5f\n",refTime);
    printf("time elapsed: %2.5f\n",refTime2);

    printf("with ITER=%llu\n",ITER);
    printf("result:%s\n",result1 == result2 ? "same":"different");
    printf("speedup = %f\n", refTime2/refTime);
    return 0;
}
void initValue(int *a,int *b,int *c,unsigned len)
{
    for(int i = 0 ; i < len ; i++)
    {
        a[i] = 0;
        b[i] = i;
        c[i] = -i;
    }

}
```
vec_loop.cpp


```c++
#pragma GCC optimize("tree-vectorize","O3")
#pragma GCC option("arch=native","tune=native","no-zeroupper") //Enable AVX
#pragma GCC target("avx")  //Enable AVX

int vec_loop(int * __restrict__ a,int* __restrict__ b,int* __restrict__ c, unsigned len,unsigned long long ITER)
{
    a = (int *)__builtin_assume_aligned(a, 16);
    b = (int *)__builtin_assume_aligned(b, 16);
    c = (int *)__builtin_assume_aligned(c, 16);

    for(int i = 0 ; i < ITER ; i++)
        for (int i=0; i<len; i++){
            a[i] = b[i] + c[i];
        }

    int k = 0;
    //reduction
    for(int i = 0; i < len ; i++)
        k += a[i];

    return k;
}

```

nloop.cpp

```c++
#pragma GCC optimize("tree-vectorize","O3")
#pragma GCC option("arch=native","tune=native","no-zeroupper") //Enable AVX
#pragma GCC target("avx")  //Enable AVX
__attribute__((optimize("no-tree-vectorize")))//force disable vectorization
int nloop(int * __restrict__ a,int* __restrict__ b,int* __restrict__ c, unsigned len,unsigned long long ITER)
{
    for(int i = 0 ; i < ITER ; i++)
        for (int i=0; i<len; i++){
            a[i] = b[i] + c[i];
        }

    int k = 0;
    //reduction
    for(int i = 0; i < len ; i++)
        k += a[i];

    return k;
}

```

Makefile

```makefile
CC = g++

OBJS =  nloop.o\
	  vec_loop.o\
	  main.o
CXXFLAGS = -g -std=c++17

all : vec-gcc

vec-gcc : ${OBJS}
	$(CC) -o $@ $(OBJS) $(CXXFLAGS) 

%.o : %.cpp
	$(CC) $< -c -fopt-info-optimized $(CXXFLAGS)
	$(CC) $< -S -fopt-info-optimized $(CXXFLAGS)

clean : 
	rm -f *.o *~ vec-gcc *.s

```

以這樣的case，大概可以得到約3倍的加速，ITER愈大，加速愈愈明顯，至少可以到15倍。

### 參考

[Auto-vectorization in GCC by GNU doc](https://gcc.gnu.org/projects/tree-ssa/vectorization.html#Example)

[Why doesn't GCC show vectorization information? --on stackoverflow](https://stackoverflow.com/questions/33758993/why-doesnt-gcc-show-vectorization-information)

[SSE & AVX Vectorization by codingame](https://www.codingame.com/playgrounds/283/sse-avx-vectorization/what-is-sse-and-avx)

[Auto-vectorization with gcc 4.7 by lockless](https://locklessinc.com/articles/vectorize/)







