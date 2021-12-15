---
layout: post
title:  "如何在 Ubuntu 18.04 （簡單）安裝OpenCV"
date:   2021-12-13 10:20:31 +0800
author : "leolijung"
categories: 
---

官方的[installation文件](https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html) 操作起來跟實際在我電腦的流程有點出入，整理了一份我的步驟，以只安裝 Core Moudule的部份當範例：

首先我假設你當前的位置是 `~` ，而且先備的package都有了（裡如g++, make, wget...）
```shell
# Install minimal prerequisites (Ubuntu 18.04 as reference)
sudo apt update && sudo apt install -y cmake g++ wget unzip
# Download and unpack sources
wget -O opencv.zip https://github.com/opencv/opencv/archive/master.zip
unzip opencv.zip
# Create build directory
mkdir -p build && cd build
# Configure
cmake  ../opencv-master
# Build
cmake --build .
```
然後你現在應該在~/build 裏面

先確認前面步驟是否成功

```
ls bin
ls lib
```
上面兩個資料夾應該存在且要滿滿的
接著，確認下面兩個檔案
```
ls OpenCVConfig*.cmake
ls OpenCVModules.cmake
```

一切順利後，
```
sudo make install
```
### 結語：
如果你會需要用到`imshow`這樣的功能，記得安裝Linux支援GUI的工具（例如GTK）
>If you are on Ubuntu or Debian, install libgtk2.0-dev and pkg-config

### Compile and Link
個人覺得直接用Cmake來編譯寫好的openCV c++主程式是蠻方便的，可以參考這篇官方文件：[Using OpenCV with gcc and CMake](https://docs.opencv.org/4.x/db/df5/tutorial_linux_gcc_cmake.html)

