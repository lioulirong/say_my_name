---
layout: post
title:  "C++ string NOTE"
date:   2021-09-12 10:20:31 +0800
categories: c++ language
---

### String比大小

使用 >, <, =, != 等operator的比較邏輯是**暗字典順序(lexicographically)**

```c++
string str1 = "cba", str2 = "abc";
bool a = str1 > str2;//true
```



> The ordering comparisons are done lexicographically -- the comparison is performed by a function equivalent to [std::lexicographical_compare](https://en.cppreference.com/w/cpp/algorithm/lexicographical_compare) or [std::lexicographical_compare_three_way](https://en.cppreference.com/w/cpp/algorithm/lexicographical_compare_three_way) (since C++20).
>
> --[cppreference-operator==,!=,<,<=,>,>=,<=>(std::basic_string)](https://en.cppreference.com/w/cpp/string/basic_string/operator_cmp)

### String 排序

有了[String 比大小](#String比大小)，表示String 可以排序，使用STL 的 algorithm裡的 sort就可以

```c++
string str = "456789123";
sort(str.begin(),str.end()); //str 已被更改
```



 





























