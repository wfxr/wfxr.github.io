---
layout: post
title: linq与foreach遍历容器效率对比
category: 技术
tags: .Net C#
description: linq与foreach两种方法遍历集合查找元素的效率对比
math: true
---

最近安装了ReSharper，每当习惯性地使用`foreach`循环时，它总是建议我将其重构为`linq`形式。虽然明白写成`linq`语句的形式更简洁，可读性也更好，但是没怎么了解过`linq`的内部实现，不清楚这么做对效率有没有大的影响，于是就做了一个简单的测试，看看`linq`和`foreach`在遍历循环方面的效率差异。

<!-- more -->

## 遍历容器的几种方式

使用`for`语句

```c#
for (var i = 0; i < samples.Count; ++i)
    if (conditions...)
        ...
```

使用`foreach`语句

```c#
foreach (var item in samples)
    if (conditions...)
        ...
```

使用`linq`语句

```c#
samples.Where(conditions...))...
```

## `linq`和`foreach`的效率对比

### 构建测试
`foreach`和`for`语句基本等价，主要测试`foreach`与`linq`的效率差异。为此编写了一个简单的`LinqTest`类来辅助测试，测试主体是下面两个函数：

```c#
private void DoLinq()
{
    var result = Samples.Where(item => Equals(item, Sought)).ToList();
}

private void DoForeach()
{
    var results = new List<object>();
    foreach (var item in Samples)
        if (Equals(item, Sought))
            results.Add(item);
}
```

这两个函数内部分别用`linq`和`foreach`的方式完成相同的功能，即在`Samples`列表中，找出和`Sought`相同的项，并将其添加到`result`列表中。

这里可能有人会问，两个函数都不需要返回什么结果，仅仅做个判断就行了，为何还要执行添加操作？
确实，之前网上看到的有些帖子就是这么做的，代码有点像这个样子：

```c#
private void DoLinq()
{
    Samples.Where(item => Equals(item, Sought));
}

private void DoForeach()
{
    foreach (var item in Samples)
        if (Equals(item, Sought));
}
```

这样做的话一定会得出linq效率比foreach好得多的结论。
原因是linq使用了延时查询技术，只有当查询结果真正用到的时候才执行查询。
为了公平比较，必须让linq查询真正被执行。怎么做呢，一个简单的办法就是对查询结果使用`ToList()`扩展函数就行（`ToArray()`和`ToDictionary()`也可以）。相应的，也需要在foreach中把匹配到的结果添加到一个`List`。 

### 测试结果
首先考察两种方式在不同数据规模下的表现。

样本数据量从800,000到51,200,000以2倍步长递增，每个样本重复测试10次，取平均值。下面是测试的结果。

* **列表元素为`int`时：**

|样本大小|8.00E+05|1.60E+06|3.20E+06|6.40E+06|1.28E+07|2.56E+07|5.12E+07|
|:------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|
|`foreach` (*ms*) |14  |29  |78  |183 |252 |630 |985 |
|`linq` (*ms*)    |17  |34  |70  |144 |284 |577 |1145|
|**相对效率增幅** |-18%|-15%|11% |27% |-11%|9%  |-14%|

![Chart of List<int>](/res/img/linq_test_result_int.jpg)

* **列表元素为`string`时**

|样本大小|8.00E+05|1.60E+06|3.20E+06|6.40E+06|1.28E+07|2.56E+07|5.12E+07|
|:------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|
|`foreach` (*ms*) |14  |28  |56  |120 |262 |507 |965 |
|`linq` (*ms*)    |17  |33  |70  |138 |296 |568 |1157|
|**相对效率增幅** |-18%|-15%|-20%|-13%|-11%|-11%|-17%|

![Chart of List<string>](/res/img/linq_test_result_string.jpg)

<p>
（相对效率增幅通过`E_(21)=(e_1 - e_2) / e_1 = (1 / t_1 - 1 / t_2) / (1 / t_1) = (t_2 - t_1) / t_2`计算。）
</p>

从上面数据可以看出，测试中`linq`比`foreach`慢15%左右。在对`List<int>`的测试中，似乎数据量为3.20E+06，6.40E+06和2.56E+07时，linq的效率高一些，不过并无一致的规律可循，可能是由于系统的进程调度和CPU的频率波动所造成的。

进一步做了数据量为1亿的测试，从结果来看，linq的效率平均为80%左右。

## 结论

简单地用`linq`来代替`foreach`在性能方面有所损失，不过总体来看`linq`给.Net程序开发带来的收益还是远大于性能上的损失。如果不是对性能有特别苛刻的要求，完全可以放心地使用`linq`，使程序逻辑更加清晰，代码也更简洁易读。对于性能是首要考虑因素的代码，可以考虑采用LINQ模式的并行实现PLINQ，或是用C/C++嵌入编写那一部分耗时的代码。
