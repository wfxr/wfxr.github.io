---
layout: post
title: 将文件或字符串中的数据读入容器的快捷方法
category: C++
tags: C++ STL Iterator
description: 介绍了一种将文本或字符串中的数据读入容器的迭代器技巧
math: true
---

## 从文件流读入：

```c++
ifstream in{"input.txt"};
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>{}};
```

<!-- more -->

## 从字符串流读入：

```c++
istringstream in{str};
vector<int> nums{istream_iterator<int>{in}, istream_iterator<int>{}};
```

这种方式对于其他形式的流同样适用。

需要注意的是，必须要先创建一个命名的流`in`。如果简单地将两行代码合并，写成下面的形式是不能通过编译的：

```c++
vector<int> nums{istream_iterator<int>{istringstream{str}}, istream_iterator<int>{}};
```

因为`istream_iterator`的构造函数接收的是左值引用类型`istream_type &`（**无`const`修饰**），而临时创建的无名对象是右值类型`istream_type &&`，二者不兼容。
