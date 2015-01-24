---
layout: post
title: linq与foreach遍历容器效率对比（二）
category: 技术
tags: .Net C#
description: linq与foreach
math: true
---

最近安装了ReSharper，每当习惯性地使用`foreach`循环时，它总是建议我将其重构为`linq`形式。虽然明白写成`linq`语句的形式更简洁，可读性也更好，但是没怎么了解过`linq`的内部实现，不清楚这么做对效率有没有大的影响，于是就做了一个简单的测试，看看`linq`和`foreach`在遍历循环方面的效率差异。

<!-- more -->
