---
layout: post
title: 获取std::unordered_map实际占用的空间大小
category: C++
tags: C++ STL unordered_map
description: 介绍了一种将文本或字符串中的数据读入容器的迭代器技巧
math: false
---

C++标准库中的`std::unordered_map`并没有给出一个接口返回其空间占用量的大小，不过我们可以通过如下方式得到一个近似值：

<!-- more -->

{% gist wfxr/2fb6810992e21d0f311d64cb88c9c1c8 %}

*需要说明的是，这里得到的real_size并非实际的物理内存占用量，而是哈希表中所有空位置和已占用位置的总数。*
