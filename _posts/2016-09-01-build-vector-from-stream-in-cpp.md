---
layout: post
title: 迭代器技巧：将文件或字符串中的数据读入C++容器的快捷方法
category: 技术
tags: C++ STL Iterator
description: 介绍了一种将文本或字符串中的数据读入容器的迭代器技巧
math: true
---

给定包含着某种类型数据的文本文件（或字符串），如何用C++将其读入到一个STL容器中？

<!-- more -->

答案是只需要两行代码：

##从文件流读入：

```c++
ifstream in{"input.txt"};
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>{}};
```

##从字符串流读入：

```c++
istringstream in{str};
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>{}};
```

需要注意的是，必须要先创建一个命名的流`in`。如果简单地将两行代码合并，写成下面的形式是不能通过编译的：

```c++
vector<int> nums{istream_iterator<int>{istringstream{str}}, istream_iterator<int>{}};
```

因为`istream_iterator`的构造函数只接收引用类型的对象，而临时创建的无名对象并不是有效的引用类型。
