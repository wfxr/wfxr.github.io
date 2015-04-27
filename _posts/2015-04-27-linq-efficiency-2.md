---
layout: post
title: linq与foreach遍历容器效率对比（二）
category: 技术
tags: .Net C#
description: linq与foreach两种方法遍历集合查找元素的效率对比
math: true
---

通过上一篇的测试我们知道，LINQ方式遍历循环在效率上要比foreach方式差一些，然而这是不是意味着在数据规模比较大，对效能要求比较高的时候我们就不应该使用LINQ语句了呢？答案是不一定。实际上，借助PLINQ技术，在多数情况下，可以使查询运行速度显著提高。本篇就简要介绍PLINQ的使用，并像前文一样给出其和LINQ及foreach的效率对比。

<!-- more -->

## 什么是PLINQ?

对PLINQ技术的详细讨论超出了本文的范畴，下面仅简要介绍其基本的原理和使用方式。
并行LINQ（即PLINQ）是LINQ模式的并行实现。PLINQ查询在许多方面都类似于非并行LINQ to
Objects查询，他们的主要区别在于PLINQ尝试充分利用系统中的所有处理器（及核心）。PLINQ利用多处理器的原理是将数据源分成片段，然后再多个处理器上对单独工作线程的每个片段进行并发的查询。通常，PLINQ的使用非常简单，只需向数据源添加AsParallel查询操作，即可在多数查询类型的旧版代码上获得显著的性能改进。

## 测试语句改进

上一篇对foreach和linq的测试中，为了保证查询真正被执行，使用了`ToList()`方法。但是这样存在的一个问题是测试数据量很大时，ToList()的执行要占用很多时间，这部分二者都要用到的时间弱化了差异的对比。所以在本文中，使用了新的测试方法，以保证尽可能接近真实的差异：

```C#
private void DoForeach()
{
    var count = 0;
    foreach (var sample in Samples) 
        if (Equals(sample, Sought)) ++count;
}
private void DoLinq()
{
    var count = Samples.Count(sample => Equals(sample, Sought));
}
private void DoPLinq() {
    var count = Samples.AsParallel().Count(sample => Equals(sample, Sought));
}
```

### 测试结果

样本数据量从10,000,000到80,000,000等步长增长，每个样本重复测试5次，取平均值。下面是测试的结果。

测试用时：

|样本大小|10M|20M|30M|40M|50M|60M|70M|80M|
|:------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|
|`foreach` (*ms*)|165|335|537|676|854|1005|1196|1330|
|`linq` (*ms*)   |246|497|749|956|1185|1482|1760|1917|
|`plinq` (*ms*)  |57|109|160|212|289|319|394|436|

![Chart of eff](/res/img/2015-04-27-linq-efficiency-2/result-eff.jpg)

相对效率：

|样本大小|10M|20M|30M|40M|50M|60M|70M|80M|
|:------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|
|`foreach` (*ms*)|100%|100%|100%|100%|100%|100%|100%|100%|
|`linq` (*ms*)   |67%|67%|72%|71%|72%|68%|68%|69%|
|`plinq` (*ms*)  |289%|307%|336%|319%|296%|315%|304%|305%|

![Chart of relative eff](/res/img/2015-04-27-linq-efficiency-2/result-relative-eff.jpg)

可以看出，`plinq`的运行效率遥遥领先于其他两种方法，平均达到了`foreach`方法的三倍。另外也可以看出，更改测试语句后的结果更真实的反应了`linq`和`foreach`之间的效率差异（原来得到的结果是85%左右）。

## 结论
综合两次的测试结果，我们对`foreach`、`linq`以及`plinq`的效率有了一个基本的认识。相信大多数人和我有共同的结论，那就是LINQ技术完全可用，它可以帮助我们写出更简洁易读的代码，而且还可以轻松地通过使用并行技术获得执行效率的大幅提升。当然，`for`语句和`foreach`语句同样可以通过并行提升效率，只是很多时候我们应该关心的不仅仅是程序的执行效率，还应该多考虑程序的编写效率。
