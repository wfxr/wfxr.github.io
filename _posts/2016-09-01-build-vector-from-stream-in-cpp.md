---
layout: post
title: 迭代器技巧：将文件或字符串中的数据读入C++容器的快捷方法
category: 技术
tags: C++ STL Iterator
description: 介绍了一种将文本或字符串中的数据读入容器的迭代器技巧
math: true
---

给定一个包含着数字的文本文件（或字符串），如何用C++将其读入到一个STL容器中？

<!-- more -->

##从文件流读入：

```c++
ifstream in{"input.txt"};
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>()};
```

##从字符串流读入：

```c++
istringstream in(str);
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>()};
```
