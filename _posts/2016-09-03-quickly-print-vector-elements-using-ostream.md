---
layout: post
title: 使用标准输出流快速打印容器元素[C++]
category: 技术
tags: C++ STL
description: 介绍了一种使用标准输出流快速打印容器元素的技巧
math: true
---

最近在重温排序算法，为了调试方便，经常需要将容器里面的元素打印在屏幕上:

```c++
cout << '[';
for (const auto& e : container)
    cout << e << ", ";
cout << ']'
```

<!-- more -->

对于`[1, 2, 3, 4, 5]`，输出的内容为：

```
[1, 2, 3, 4, 5,]
```

这样最后一个元素后面会多出一个','，虽然无伤大雅，但看着总有些别扭。

下面介绍一种比较优雅的解决方案：

```c++
template <typename T>
ostream &operator<<(ostream &os, const vector<T> &v) {
    os << '[';
    char comma[]{'\0', ' ', '\0'};
    for (const auto &e : v) {
        os << comma << e;
        comma[0] = ','
    }
    return os << ']';
}
```

重载了流输出运算符以后，就可以像打印普通的变量一样显示容器中的元素了：

```c++
vector<int> iv { 1, 2, 3, 4 };
cout << iv << endl;

vector<string> sv {"Bob", "Jane", "Tom"};
cout << sv << endl;
```

输出分别是：

```
[1, 2, 3, 4]
[Bob, Jane, Tom]
```

是不是感觉清爽很多呢？

用在**GoogleTest**中的效果：

```c++
TEST(Test, MergeSort) {
    auto v = vec;
    merge_sort(v);
    ASSERT_TRUE(is_sorted(v.begin(), v.end()))
        << endl << "before: " << vec
        << endl << " after: " << v;
}
```

测试失败时的输出：

```
[ RUN      ] Test.MergeSort
sort_test.cpp:39: Failure
Value of: is_sorted(v.begin(), v.end())
  Actual: false
  Expected: true

  before: [5, 2, 9, 5, 2, 4, 8, 41, 1, 3, 65, 85, 89, 5, 14, 52, 362, 32]
   after: [2, 65, 85, 52, 14, 362, 32, 2, 8, 3, 5, 41, 1, 9, 5, 89, 4, 5]
[  FAILED  ] Test.MergeSort (0 ms)
```
